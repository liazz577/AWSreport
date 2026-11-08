---
title: "Tạo API"
date: 2026-09-07
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

Trang web cần ba thao tác, cùng do một hàm Lambda API xử lý:

| Route | Mục đích |
| --- | --- |
| `POST /uploads` | Kiểm tra file, ghi bản ghi `PENDING`, trả về presigned POST |
| `GET /images` | Liệt kê 50 ảnh mới nhất kèm link xem có hạn 1 giờ |
| `DELETE /images/{id}` | Xoá ảnh gốc, thumbnail và bản ghi |

#### Nội dung
1. [Tạo Lambda API](5.6.1-api-lambda/)
2. [Tạo HTTP API](5.6.2-http-api/)
