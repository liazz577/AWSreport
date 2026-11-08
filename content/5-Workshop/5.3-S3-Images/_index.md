---
title: "Create the Image Bucket"
date: 2026-09-07
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

## Create the bucket
1. **Amazon S3 → Create bucket**, type **General purpose**.
2. **Bucket name:** `snapthumb-images-<your-name>` (bucket names are globally unique).
3. Keep **Block all public access** checked: images are only shown through time-limited links.
4. **Create bucket**.

## Create the two folders
Open the bucket → **Create folder** → `uploads`, then again → `thumbnails`.

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Insert screenshot:</b> bucket with uploads/ and thumbnails/ → <code>static/images/5-Workshop/5.3/01-bucket.png</code></p>
<!-- ![bucket with uploads/ and thumbnails/](/images/5-Workshop/5.3/01-bucket.png?width=90pc) -->

## Configure CORS
The browser uploads straight to this bucket, which is a different address from the website, so the bucket must allow it.

**Permissions** tab → **Cross-origin resource sharing (CORS)** → **Edit** → paste → **Save changes**.

```json
[
  {
    "AllowedOrigins": ["*"],
    "AllowedMethods": ["POST", "GET"],
    "AllowedHeaders": ["*"],
    "MaxAgeSeconds": 3600
  }
]
```

{{% notice tip %}}
`"*"` allows any website, which is fine for a demo. In production, put your website's address there.
{{% /notice %}}
