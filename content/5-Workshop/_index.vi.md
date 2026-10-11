---
title: "Workshop"
date: 2026-09-07
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Xây dựng StudyMate: chatbot hỏi đáp Sổ tay sinh viên với Amazon Bedrock

#### Tổng quan

Trong workshop này, bạn sẽ dựng **StudyMate** từ đầu bằng AWS Management Console. Sinh viên gõ câu hỏi trên trang web; hệ thống tìm đoạn liên quan trong các file PDF của trường rồi để mô hình AI viết câu trả lời ngắn gọn kèm tên file nguồn.

Bạn chỉ phải viết hai file nhỏ: một hàm Lambda Python khoảng 40 dòng và một trang HTML. Phần còn lại là cấu hình trên console.

![Kiến trúc StudyMate](/images/5-Workshop/architecture-vi.svg?width=100pc)

| | |
| --- | --- |
| **Thời gian** | Khoảng 2 giờ |
| **Mức độ** | Cơ bản (chỉ cần tài khoản AWS và một IAM user) |
| **Chi phí** | Dưới 1 USD nếu dọn dẹp sau khi làm xong |
| **Region** | US East (N. Virginia) `us-east-1` |

#### Nội dung

1. [Tổng quan workshop](5.1-workshop-overview/)
2. [Chuẩn bị](5.2-prerequiste/)
3. [Tải tài liệu lên Amazon S3](5.3-s3-documents/)
4. [Tạo Bedrock Knowledge Base](5.4-knowledge-base/)
5. [Tạo hàm Lambda](5.5-lambda/)
6. [Tạo API bằng API Gateway](5.6-api-gateway/)
7. [Đưa giao diện web lên S3](5.7-frontend/)
8. [Kiểm thử và giám sát](5.8-testing-monitoring/)
9. [Dọn dẹp tài nguyên](5.9-cleanup/)
