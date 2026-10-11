---
title: "Worklog Tuần 8"
date: 2026-10-26
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

### Mục tiêu tuần 8:

* Viết hàm Lambda gọi Knowledge Base và cấp quyền IAM tối thiểu.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Đọc tài liệu API retrieve_and_generate của boto3. | 26/10/2026 | 26/10/2026 | <https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/bedrock-agent-runtime.html> |
| 3 | - Viết hàm Lambda studymate-ask. | 27/10/2026 | 27/10/2026 | - |
| 4 | - Viết IAM inline policy chỉ cho phép gọi đúng Knowledge Base và mô hình. | 28/10/2026 | 28/10/2026 | <https://docs.aws.amazon.com/IAM/> |
| 5 | - Sửa lỗi AccessDeniedException và timeout, đọc log CloudWatch. | 29/10/2026 | 29/10/2026 | - |
| 6 | - Dịch blog 2.<br>- Viết worklog tuần 8. | 30/10/2026 | 30/10/2026 | <https://aws.amazon.com/blogs/> |

### Kết quả đạt được tuần 8:

* Hàm Lambda trả về JSON gồm câu trả lời và danh sách nguồn.
* Biết đọc log để tìm nguyên nhân lỗi.
