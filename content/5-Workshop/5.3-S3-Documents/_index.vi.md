---
title: "Tải tài liệu lên S3"
date: 2026-09-07
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

## Amazon S3 là gì?

Amazon S3 là **ổ lưu trữ trên mạng** của AWS, giống Google Drive. **Bucket** là một thư mục lớn; mỗi file bên trong gọi là một **object**. Bucket này để **riêng tư** vì chỉ Knowledge Base cần đọc nó.

## Tạo bucket

1. Mở console **Amazon S3** → **Create bucket**.
2. **Bucket type:** General purpose.
3. **Bucket name:** `studymate-docs-<ten-ban>` (tên bucket phải là duy nhất trên toàn AWS nên hãy thêm tên bạn).
4. Giữ nguyên ô **Block all public access** đang được chọn.
5. Các mục khác để mặc định → **Create bucket**.

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Chèn ảnh:</b> form Create bucket → <code>static/images/5-Workshop/5.3/01-create-bucket.png</code></p>
<!-- ![form Create bucket](/images/5-Workshop/5.3/01-create-bucket.png?width=90pc) -->

## Tải file PDF lên

1. Mở bucket vừa tạo → **Upload**.
2. Kéo thả các file PDF vào → **Upload**.

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Chèn ảnh:</b> danh sách file PDF trong bucket → <code>static/images/5-Workshop/5.3/02-objects.png</code></p>
<!-- ![danh sách file PDF trong bucket](/images/5-Workshop/5.3/02-objects.png?width=90pc) -->

{{% notice tip %}}
Đặt tên file dễ hiểu, ví dụ `quy-che-dao-tao.pdf`. Chatbot hiển thị tên file làm nguồn, nên tên rõ ràng giúp người dùng tin câu trả lời hơn.
{{% /notice %}}
