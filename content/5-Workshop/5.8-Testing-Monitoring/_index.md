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
Record 5 real photos in this table: <span style="background:#fff3c4">[fill in your results]</span>

| Photo | Original | Thumbnail | Saved | Processing time |
| --- | --- | --- | --- | --- |
| 1 | | | | |

## Monitoring
1. **Lambda → snapthumb-thumbnail → Monitor**: invocations, duration, errors.
2. **CloudWatch → Log groups → /aws/lambda/snapthumb-thumbnail**: one log line per image with sizes before and after.

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Insert screenshot:</b> Monitor tab of the Thumbnail Lambda → <code>static/images/5-Workshop/5.8/01-monitor.png</code></p>
<!-- ![Monitor tab of the Thumbnail Lambda](/images/5-Workshop/5.8/01-monitor.png?width=90pc) -->

## Common errors

| Symptom | Cause | Fix |
| --- | --- | --- |
| `No module named 'PIL'` in the log | Layer not attached, or built for the wrong platform | Attach `pillow-py312` (5.5.2); rebuild with the exact command in 5.5.1 |
| Stuck at "Processing" | Missing S3 trigger or wrong prefix | Check the trigger has prefix `uploads/` |
| `AccessDenied` in the log | Inline policy has the wrong bucket name or account ID | Fix `YOURNAME` / `ACCOUNT_ID` |
| CORS error in the browser on upload | Bucket CORS missing | Add the CORS rules (5.3) |
| CORS error on `/images` | API CORS missing | Configure CORS on the API (5.6.2) |
| S3 answers 403 on upload | File over 10 MB or link expired (5 min) | Retry with a smaller file |
