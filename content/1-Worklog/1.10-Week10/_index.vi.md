---
title: "Worklog Tuần 10"
date: 2026-11-09
weight: 10
chapter: false
pre: " <b> 1.10. </b> "
---

### Mục tiêu tuần 10:

* Dựng lại toàn bộ hệ thống bằng code và viết test.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Viết template CloudFormation cho toàn bộ hạ tầng. | 09/11/2026 | 09/11/2026 | <https://docs.aws.amazon.com/cloudformation/> |
| 3 | - Xử lý phụ thuộc vòng giữa S3 notification và Lambda permission. | 10/11/2026 | 10/11/2026 | <https://docs.aws.amazon.com/cloudformation/> |
| 4 | - Viết script deploy.sh và destroy.sh. | 11/11/2026 | 11/11/2026 | - |
| 5 | - Viết test toàn luồng với moto (AWS giả lập). | 12/11/2026 | 12/11/2026 | <https://docs.getmoto.org/> |
| 6 | - Dịch blog 3.<br>- Viết worklog tuần 10. | 13/11/2026 | 13/11/2026 | <https://aws.amazon.com/blogs/> |

### Kết quả đạt được tuần 10:

* Một lệnh deploy.sh dựng xong hệ thống trong vài phút.
* 7 test tự động đều qua.
