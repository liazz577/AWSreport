---
title: "Testing and Monitoring"
date: 2026-09-07
weight: 8
chapter: false
pre: " <b> 5.8. </b> "
---

## Functional tests

| # | Test | Expected |
| --- | --- | --- |
| 1 | Upload a phone photo (JPEG) | `DONE` within a few seconds, thumbnail 320 px on the long edge |
| 2 | Upload a portrait photo | Thumbnail is upright and taller than wide |
| 3 | Upload a transparent PNG | Thumbnail has a white background |
| 4 | Upload 5 images at once | All five finish; Lambda runs them in parallel |
| 5 | Upload a `.txt` renamed to `.png` | Card shows `FAILED` with the error |
| 6 | Choose a 12 MB image | Page refuses it before upload |
| 7 | Delete an image | Card disappears; both files are gone from S3 |

## Measurements
Measured on AWS (ap-southeast-1, 18/11/2026). Six test images were uploaded **at the same time** from a client that uses the API and presigned POST exactly like the web page.

| Image | Original | Thumbnail | File size | Saved | Processing time |
| --- | --- | --- | --- | --- | --- |
| Landscape JPEG | 4032×3024 | 320×240 | 6.1 MB → 12.7 KB | 99.8% | 2,686 ms |
| Portrait JPEG (EXIF rotate 90°) | 3000×4000 | 240×320, upright | 5.4 MB → 12.5 KB | 99.8% | 2,778 ms |
| Transparent PNG | 1200×1200 | 320×320, white background | 11.4 KB → 5.2 KB | 53.9% | 2,547 ms |
| WebP | 1920×1080 | 320×180 | 818 KB → 16.4 KB | 98.0% | 2,763 ms |
| Small JPEG | 200×150 | 200×150 (not enlarged) | 21.0 KB → 14.9 KB | 29.1% | 1,076 ms |
| Text file renamed `.png` | – | – | – | – | `FAILED`: *cannot identify image file* |

**Cold start vs warm.** The times above include S3 event delivery and a *cold start*: the first run of each Lambda instance spends about **0.4 s** loading Python and Pillow (`Init Duration` in the log). On an instance that is already warm, a 6 MB photo took **0.48 s** of function time. Peak memory was **213 MB** out of 1024 MB; the extra memory is kept because Lambda gives more CPU with more memory.

| Other check | Result |
| --- | --- |
| API rejects GIF / declared size 11 MB | `400` |
| Client declares 1 KB but sends 10.5 MB | S3 refuses: `400 EntityTooLarge` |
| CORS preflight `OPTIONS /uploads` | `204`, `Access-Control-Allow-Origin: *` |
| Image uploaded straight to S3 (step 5.5) then `GET /images` | `200`, image listed; `DELETE` → `204`, both files removed |

{{% notice tip %}}
Repeat the table with your own phone photos: the page shows the size, savings and processing time on each card.
{{% /notice %}}

## Monitoring
1. **Lambda → snapthumb-thumbnail → Monitor**: invocations, duration, errors.
2. **CloudWatch → Log groups → /aws/lambda/snapthumb-thumbnail**: one log line per image with sizes before and after.
3. Log groups that Lambda creates by itself never expire. If you built by hand, select both `/aws/lambda/snapthumb-…` groups → **Actions → Edit retention setting(s)** → **2 weeks**, so logs do not pile up (the CloudFormation template already sets 14 days).
4. **CloudFront → Monitoring** (or **View metrics** on the distribution page): requests, data transferred and the share of 4xx/5xx errors for the web page.

![Monitor tab of the Thumbnail Lambda](/images/5-Workshop/5.8/01-monitor.png?width=90pc)

## Alerts (CloudWatch alarms + Amazon SNS)

Watching the Monitor tab only works while someone is looking at it. Three alarms send an email as soon as something goes wrong.

{{% notice info %}}
**Who receives the alerts:** only the **administrator / operator** of the system, i.e. the person who deployed SnapThumb, not the people who upload images. The purpose is to let the admin notice and fix problems (a crashing function, server errors, abuse with fake files) without watching the console all day. End users never receive these emails and cannot subscribe themselves.

- The recipient is the email given when deploying (`ALARM_EMAIL`) or subscribed by hand to the SNS topic. Whoever follows this workshop uses **their own** email.
- To add another admin (for example a mentor or teammate): **Amazon SNS → Topics → the alarm topic → Create subscription → Email**. That person must click **Confirm subscription** before receiving anything, so nobody is added without agreeing.
{{% /notice %}}


| Alarm | Metric | Fires when | What it catches |
| --- | --- | --- | --- |
| `snapthumb-thumbnail-errors` | `AWS/Lambda` → `Errors` of the thumbnail function | ≥ 1 in 5 minutes | Crash, timeout or out-of-memory in the thumbnail function |
| `snapthumb-api-5xx` | `AWS/ApiGateway` → `5xx` of the HTTP API | ≥ 1 in 5 minutes | The API Lambda failed and users see "Internal error" |
| `snapthumb-failed-images` | `SnapThumb` → `FailedImages` (metric filter on the log line `Failed to process`) | ≥ 3 in 5 minutes | Many corrupt or fake image files at once (abuse or a broken client) |

The thumbnail function catches bad files and marks them `FAILED`, so the built-in `Errors` metric stays at 0 for them. The **metric filter** counts those log lines and turns them into the `FailedImages` metric.

#### With CloudFormation (5.9)

The template already contains the SNS topic, the metric filter and the three alarms. Pass your email when deploying, then click **Confirm subscription** in the email from AWS:

```bash
ALARM_EMAIL=you@example.com bash deploy.sh
```

#### By hand in the console

1. **Amazon SNS → Topics → Create topic**: type **Standard**, name `snapthumb-alarms`. Then **Create subscription**: protocol **Email**, your address. Confirm the email.
2. **CloudWatch → Log groups → `/aws/lambda/snapthumb-thumbnail` → Metric filters → Create metric filter**: pattern `"Failed to process"`, namespace `SnapThumb`, metric name `FailedImages`, metric value `1`, default value `0`.
3. **CloudWatch → Alarms → Create alarm** three times, using the table above: statistic **Sum**, period **5 minutes**, condition **Greater/Equal**, notification to `snapthumb-alarms` for both **In alarm** and **OK**. Under *Missing data treatment* choose **Treat missing data as good (not breaching threshold)**.

#### Test the alert

Upload three fake images (text files renamed to `.png`) on the web page. Within a few minutes `snapthumb-failed-images` turns **In alarm** and the email arrives; when no new failures arrive it returns to **OK** and a second email confirms it.

Result on AWS (ap-southeast-1, 22/11/2026): three fake images were uploaded; `snapthumb-failed-images` went from **OK** to **In alarm** after **71 seconds**, and CloudWatch reported *Successfully executed action* on the SNS topic, which delivered the email. With no new failures it returned to **OK** 15 minutes later and sent the second email.

![CloudWatch alarms: snapthumb-failed-images In alarm](/images/5-Workshop/5.8/02-alarm.png?width=90pc)

## Common errors

| Symptom | Cause | Fix |
| --- | --- | --- |
| `No module named 'PIL'` in the log | Layer not attached, or built for the wrong platform | Attach `pillow-py312` (5.5.2); rebuild with the exact command in 5.5.1 |
| Stuck at "Processing" | Missing S3 trigger or wrong prefix | Check the trigger has prefix `uploads/` |
| `AccessDenied` in the log | Inline policy has the wrong bucket name or account ID | Fix `YOURNAME` / `ACCOUNT_ID` |
| CORS error in the browser on upload | Bucket CORS missing | Add the CORS rules (5.3) |
| CORS error on `/images` | API CORS missing | Configure CORS on the API (5.6.2) |
| S3 answers `400 EntityTooLarge` on upload | File larger than 10 MB (the presigned POST allows at most 10 MB) | Choose a smaller file |
| S3 answers `403` on upload | Upload link expired (valid 5 minutes) | Upload again: the page asks for a new link |
| A card stays at "Processing" after a failed upload | Old page version kept the `PENDING` record | Use the current `index.html`: it deletes the record when the upload fails and stops polling after 2 minutes |
| No alert email arrives | Subscription not confirmed, or the email went to spam | **SNS → Subscriptions**: status must be *Confirmed*; check the spam folder |
| An "OK" alert email right after creating the alarms | Each alarm moves from *Insufficient data* to *OK* once | Normal, nothing to do |
| The CloudFront link shows `AccessDenied` | Bucket policy is missing or has the wrong distribution ID, or *Default root object* is empty | Copy the policy again from the distribution's **Origins → Edit** page; set *Default root object* to `index.html` (5.7) |
| The page still shows the old version or the *demo mode* badge | CloudFront serves the cached files | **Invalidations → Create invalidation** → `/*`, then reload; check that `config.js` is in the bucket |
| The distribution stays at *Deploying* | A new or changed distribution takes a few minutes to reach every edge location | Wait 5–10 minutes |
| The console shows no table, function or alarm | Another region is selected | Choose **Asia Pacific (Singapore)** at the top right (S3 lists buckets from every region, other services do not) |

## Challenges and solutions

| Challenge | What happened | Solution | Lesson |
| --- | --- | --- | --- |
| Project idea blocked by quota | The first idea (a chatbot on Amazon Bedrock) could not run because the new account's quota was too low | Switched to SnapThumb, which uses only core services with generous limits | Check service quotas before choosing an architecture |
| Risk of an infinite loop | The thumbnail function writes to the same bucket that triggers it | Trigger only on the `uploads/` prefix and write to `thumbnails/` | Separate input and output prefixes (or buckets) for S3-triggered Lambda |
| Circular dependency in CloudFormation | The bucket needs the Lambda permission, and the permission needs the bucket ARN | Build the bucket name from the stack name and account ID, and make the bucket depend on the permission | Use predictable names to break cycles |
| Pillow does not run on Lambda when installed on a laptop | Wheels built for Windows or macOS do not load on Amazon Linux | `pip install --platform manylinux… --python-version 3.12 --only-binary=:all:` in CloudShell | Package native libraries for the Lambda runtime and architecture |
| Bug found only on real AWS | An image uploaded from the S3 console had no record from the API, so `GET /images` crashed with a 500 | Fill missing fields with `if_not_exists`, read optional fields with `.get()`, add 2 regression tests | Mocks pass only what you thought of; test end to end on the real service |
| Website bucket had to be public | The first version used the S3 static website endpoint: HTTP only, and the bucket needed a public policy | Moved to CloudFront + Origin Access Control in two stack updates (first add the distribution next to the public site, test it, then lock the bucket), so the site never went offline | Keep buckets private and put CloudFront in front; change live systems in small, tested steps |
| Users lying about file size | The browser declares the size; a client could send a bigger file | `content-length-range` in the presigned POST policy: S3 itself rejects files over 10 MB (`400 EntityTooLarge`) | Enforce limits on the server side, not only in the UI |

## Limitations and next steps

SnapThumb is a demo. These are known limitations and how AWS services would address them:

| Limitation | Risk | Next step |
| --- | --- | --- |
| The API has no sign-in | Anyone who knows the API address can list and **delete every image** | Add an **Amazon Cognito** user pool and a JWT authorizer on the HTTP API; store `userId` with each image |
| No firewall in front of the website and API | Bots can use up the API's throttling limit (real users then get `429`) or flood the page | Attach **AWS WAF** rate-based rules to the CloudFront distribution. HTTP APIs cannot use WAF directly, so also route the API through CloudFront (for example a `/api/*` behavior) or switch to a REST API |
| `GET /images` scans the whole table | Gets slower and costlier as images grow | Add a GSI on `createdAt` (or per-user key) and use `Query` with pagination |
| A failed thumbnail is not retried | A temporary error leaves the image `FAILED` | Configure an on-failure destination / **SQS dead-letter queue** and retry |
