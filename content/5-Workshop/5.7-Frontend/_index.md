---
title: "Host the Web Frontend on S3"
date: 2026-09-07
weight: 7
chapter: false
pre: " <b> 5.7. </b> "
---

## Overview

The web page is a single `index.html` with an input box, a Send button and a little JavaScript that calls the API. S3 can serve static websites: put the file in a bucket, enable website hosting, and allow public read access.

{{% notice note %}}
This is a **second** bucket. The documents bucket from 5.3 stays private; only this website bucket is public.
{{% /notice %}}

## Step 1: Create the website bucket

1. **S3** → **Create bucket** → name `studymate-web-<your-name>`.
2. **Uncheck** **Block all public access** and tick the acknowledgement box.
3. **Create bucket**.

## Step 2: Enable static website hosting

1. Open the bucket → **Properties** tab → scroll to **Static website hosting** → **Edit**.
2. **Enable**, **Index document:** `index.html` → **Save changes**.

## Step 3: Add the bucket policy

**Permissions** tab → **Bucket policy** → **Edit** → paste the policy (replace the bucket name) → **Save changes**.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadForWebsite",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::studymate-web-YOURNAME/*"
    }
  ]
}
```

## Step 4: Create and upload index.html

Create `index.html` on your computer with the content below. Replace `API_URL` with your Invoke URL followed by `/ask`.

```html
<!doctype html>
<html lang="vi">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>StudyMate</title>
  <style>
    body { font-family: system-ui, sans-serif; max-width: 680px; margin: 40px auto; padding: 0 16px; }
    #log div { margin: 12px 0; padding: 10px 14px; border-radius: 10px; }
    .q { background: #e2f1ed; }
    .a { background: #f3f4f4; white-space: pre-wrap; }
    .src { font-size: 12px; color: #667; margin-top: 6px; }
    form { display: flex; gap: 8px; }
    input { flex: 1; padding: 10px; }
  </style>
</head>
<body>
  <h1>StudyMate</h1>
  <p>Hỏi về quy chế đào tạo và sổ tay sinh viên.</p>
  <div id="log"></div>
  <form id="f">
    <input id="q" placeholder="Ví dụ: Điều kiện xét học bổng?" required maxlength="500">
    <button>Gửi</button>
  </form>
  <script>
    const API_URL = "https://xxxx.execute-api.us-east-1.amazonaws.com/ask"; // thay bằng Invoke URL + /ask

    const log = document.getElementById("log");
    function add(cls, text) {
      const d = document.createElement("div");
      d.className = cls;
      d.textContent = text;
      log.appendChild(d);
      return d;
    }

    document.getElementById("f").addEventListener("submit", async (e) => {
      e.preventDefault();
      const input = document.getElementById("q");
      const question = input.value.trim();
      input.value = "";
      add("q", question);
      const box = add("a", "Đang tìm câu trả lời...");
      try {
        const res = await fetch(API_URL, {
          method: "POST",
          headers: { "content-type": "application/json" },
          body: JSON.stringify({ question })
        });
        const data = await res.json();
        box.textContent = data.answer || data.error;
        if (data.sources && data.sources.length) {
          const s = document.createElement("div");
          s.className = "src";
          s.textContent = "Nguồn: " + data.sources.map(u => u.split("/").pop()).join(", ");
          box.appendChild(s);
        }
      } catch (err) {
        box.textContent = "Không gọi được API: " + err.message;
      }
    });
  </script>
</body>
</html>
```

Upload `index.html` to the bucket. Then go to **Properties** → **Static website hosting** and open the **Bucket website endpoint**.

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Insert screenshot:</b> StudyMate page answering a question with sources → <code>static/images/5-Workshop/5.7/01-website.png</code></p>
<!-- ![StudyMate page answering a question with sources](/images/5-Workshop/5.7/01-website.png?width=90pc) -->

{{% notice tip %}}
The S3 website endpoint uses plain HTTP. As an extension, put **Amazon CloudFront** in front of the bucket to get HTTPS and keep the bucket private.
{{% /notice %}}
