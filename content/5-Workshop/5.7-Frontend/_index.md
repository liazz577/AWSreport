---
title: "Host the Web Frontend"
date: 2026-09-07
weight: 7
chapter: false
pre: " <b> 5.7. </b> "
---

## Step 1: Website bucket
1. **S3 → Create bucket** `snapthumb-web-<your-name>`, **uncheck** *Block all public access* and acknowledge.
2. **Properties → Static website hosting → Edit → Enable**, index document `index.html`.
3. **Permissions → Bucket policy:**

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadForWebsite",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::snapthumb-web-YOURNAME/*"
    }
  ]
}
```

{{% notice warning %}}
If saving the bucket policy fails with **Access denied**, *Block Public Access* is turned on for the whole account. Open **S3 → Block Public Access settings for this account → Edit**, untick the two settings about *bucket policies*, save, then add the policy again.
{{% /notice %}}

## Step 2: Upload the page
1. Download the source package [snapthumb.zip](/files/snapthumb.zip) and take `web/index.html` from it.
2. Create `config.js` next to it with your Invoke URL:

```js
window.SNAPTHUMB_CONFIG = { apiUrl: "https://abc123.execute-api.ap-southeast-1.amazonaws.com" };
```

3. Upload both files to the bucket and open the **Bucket website endpoint** from **Properties**.

## How the page uses the API

1. For each chosen file it calls `POST /uploads` and receives `upload.url` and `upload.fields`.
2. It builds a `FormData` with every field, then the file **last** (S3 requires this order), and posts it to `upload.url`.
3. It calls `GET /images` every 1.5 seconds while any image is `PENDING`, and shows the thumbnail when it turns `DONE`.

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Insert screenshot:</b> SnapThumb page showing finished thumbnails → <code>static/images/5-Workshop/5.7/01-website.png</code></p>
<!-- ![SnapThumb page showing finished thumbnails](/images/5-Workshop/5.7/01-website.png?width=90pc) -->

{{% notice tip %}}
Without `config.js` the page runs in demo mode and shrinks images in the browser. The badge at the top says which mode you are in.
{{% /notice %}}
