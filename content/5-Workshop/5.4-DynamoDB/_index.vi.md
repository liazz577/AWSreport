---
title: "Tạo bảng DynamoDB"
date: 2026-09-07
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

## Tạo bảng
1. **Amazon DynamoDB → Tables → Create table**.
2. **Table name:** `snapthumb-images`.
3. **Partition key:** `id`, kiểu **String**.
4. **Table settings:** **Customize settings** → **Capacity mode: On-demand**.
5. **Create table**.

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Chèn ảnh:</b> bảng snapthumb-images ở trạng thái Active → <code>static/images/5-Workshop/5.4/01-table.png</code></p>
<!-- ![bảng snapthumb-images ở trạng thái Active](/images/5-Workshop/5.4/01-table.png?width=90pc) -->

## Một bản ghi trông như thế nào

| Thuộc tính | Ví dụ | Do ai ghi |
| --- | --- | --- |
| `id` | `3f9c…` | Lambda API |
| `filename`, `contentType`, `originalKey`, `createdAt` | `anh-lop.jpg`, `image/jpeg`, `uploads/3f9c….jpg` | Lambda API |
| `status` | `PENDING` → `DONE` hoặc `FAILED` | Lambda API, sau đó Lambda Thumbnail |
| `thumbKey`, `originalWidth/Height`, `thumbWidth/Height`, `originalBytes`, `thumbBytes`, `processingMs` | `thumbnails/3f9c….jpg`, 4000×3000, 320×240 … | Lambda Thumbnail |

DynamoDB không cố định cấu trúc: chỉ khai báo `id`, các thuộc tính khác được thêm khi ghi.
