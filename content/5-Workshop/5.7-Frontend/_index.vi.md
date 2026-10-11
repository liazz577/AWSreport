---
title: "Đưa giao diện web lên S3"
date: 2026-09-07
weight: 7
chapter: false
pre: " <b> 5.7. </b> "
---

## Tổng quan

Trang web chỉ là một file `index.html` gồm ô nhập câu hỏi, nút Gửi và một đoạn JavaScript gọi API. S3 có chức năng host website tĩnh: bạn bỏ file vào bucket, bật chức năng này và cho phép đọc công khai.

{{% notice note %}}
Đây là bucket **thứ hai**. Bucket tài liệu ở mục 5.3 vẫn để riêng tư; chỉ bucket web này là công khai.
{{% /notice %}}

## Bước 1: Tạo bucket cho website

1. **S3** → **Create bucket** → tên `studymate-web-<ten-ban>`.
2. **Bỏ chọn** **Block all public access** và tick ô xác nhận.
3. **Create bucket**.

## Bước 2: Bật static website hosting

1. Mở bucket → tab **Properties** → kéo xuống **Static website hosting** → **Edit**.
2. **Enable**, **Index document:** `index.html` → **Save changes**.

## Bước 3: Thêm bucket policy

Tab **Permissions** → **Bucket policy** → **Edit** → dán policy (thay tên bucket) → **Save changes**.

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

## Bước 4: Tạo và tải lên index.html

Tạo file `index.html` trên máy với nội dung dưới đây. Thay `API_URL` bằng Invoke URL của bạn cộng thêm `/ask`.

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

Tải `index.html` lên bucket. Sau đó vào **Properties** → **Static website hosting** và mở **Bucket website endpoint**.

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Chèn ảnh:</b> trang StudyMate đang trả lời câu hỏi kèm nguồn → <code>static/images/5-Workshop/5.7/01-website.png</code></p>
<!-- ![trang StudyMate đang trả lời câu hỏi kèm nguồn](/images/5-Workshop/5.7/01-website.png?width=90pc) -->

{{% notice tip %}}
Địa chỉ website của S3 dùng HTTP thường. Nếu muốn mở rộng, hãy đặt **Amazon CloudFront** phía trước bucket để có HTTPS và giữ bucket ở chế độ riêng tư.
{{% /notice %}}
