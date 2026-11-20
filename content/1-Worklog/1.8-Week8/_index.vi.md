---
title: "Worklog Tuần 8"
date: 2026-10-26
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

### Mục tiêu tuần 8:

* Nối S3 với Lambda để ảnh mới tự động được thu nhỏ.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Tạo hàm Lambda Thumbnail, gắn layer Pillow. | 21/09/2026 | 21/09/2026 | <https://docs.aws.amazon.com/lambda/> |
| 3 | - Thêm trigger S3 với tiền tố uploads/. | 22/09/2026 | 22/09/2026 | <https://docs.aws.amazon.com/lambda/latest/dg/with-s3.html> |
| 4 | - Viết IAM policy quyền tối thiểu cho hàm. | 23/09/2026 | 23/09/2026 | <https://docs.aws.amazon.com/IAM/> |
| 5 | - Sửa lỗi vòng lặp vô hạn khi ghi thumbnail cùng bucket (dùng tiền tố riêng). | 24/09/2026 | 24/09/2026 | <https://docs.aws.amazon.com/lambda/latest/dg/with-s3.html> |
| 6 | - Dịch blog 2.<br>- Viết worklog tuần 8. | 25/09/2026 | 25/09/2026 | <https://aws.amazon.com/blogs/compute/> |

### Kết quả đạt được tuần 8:

* Upload ảnh lên uploads/ là có thumbnail trong thumbnails/ sau 1–3 giây.
* Đọc được log CloudWatch để tìm lỗi.
