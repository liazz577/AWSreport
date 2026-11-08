---
title: "Worklog Tuần 7"
date: 2026-10-19
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

### Mục tiêu tuần 7:

* Tạo bucket ảnh, bảng DynamoDB và đóng gói Pillow cho Lambda.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Tạo bucket S3 private, cấu hình CORS. | 19/10/2026 | 19/10/2026 | <https://docs.aws.amazon.com/AmazonS3/latest/userguide/cors.html> |
| 3 | - Tạo bảng DynamoDB chế độ on-demand. | 20/10/2026 | 20/10/2026 | <https://docs.aws.amazon.com/dynamodb/> |
| 4 | - Tìm hiểu Lambda layer, đóng gói Pillow cho Python 3.12. | 21/10/2026 | 21/10/2026 | <https://docs.aws.amazon.com/lambda/latest/dg/chapter-layers.html> |
| 5 | - Viết hàm tạo thumbnail chạy thử trên máy. | 22/10/2026 | 22/10/2026 | <https://pillow.readthedocs.io/> |
| 6 | - Dịch blog 1.<br>- Viết worklog tuần 7. | 23/10/2026 | 23/10/2026 | <https://aws.amazon.com/blogs/compute/> |

### Kết quả đạt được tuần 7:

* Có bucket, bảng DynamoDB và layer Pillow.
* Hàm tạo thumbnail chạy đúng với JPEG, PNG trong suốt và ảnh dọc.
