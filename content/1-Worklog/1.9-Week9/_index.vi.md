---
title: "Worklog Tuần 9"
date: 2026-11-02
weight: 9
chapter: false
pre: " <b> 1.9. </b> "
---

### Mục tiêu tuần 9:

* Cho người dùng upload ảnh và xem kết quả trên trình duyệt.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Viết Lambda API: tạo presigned POST, liệt kê, xoá ảnh. | 28/09/2026 | 28/09/2026 | <https://docs.aws.amazon.com/AmazonS3/latest/userguide/PresignedUrlUploadObject.html> |
| 3 | - Tạo HTTP API với 3 route, cấu hình CORS. | 29/09/2026 | 29/09/2026 | <https://docs.aws.amazon.com/apigateway/> |
| 4 | - Viết giao diện kéo thả và thư viện ảnh. | 30/09/2026 | 30/09/2026 | - |
| 5 | - Host giao diện trên S3 static website. | 01/10/2026 | 01/10/2026 | <https://docs.aws.amazon.com/AmazonS3/latest/userguide/WebsiteHosting.html> |
| 6 | - Demo nội bộ với mentor.<br>- Viết worklog tuần 9. | 02/10/2026 | 02/10/2026 | - |

### Kết quả đạt được tuần 9:

* Ứng dụng chạy đầu-cuối: upload, xem thumbnail, xoá.
* Ảnh đi thẳng từ trình duyệt lên S3, không qua Lambda.
