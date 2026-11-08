---
title: "Blog 2"
date: 2026-09-07
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

# Resize Images on the Fly with Amazon S3, AWS Lambda, and Amazon API Gateway

| Mục | Chi tiết |
| --- | --- |
| **Bài gốc** | [Resize Images on the Fly with Amazon S3, AWS Lambda, and Amazon API Gateway](https://aws.amazon.com/blogs/compute/resize-images-on-the-fly-with-amazon-s3-aws-lambda-and-amazon-api-gateway/) |
| **Nguồn** | AWS Compute Blog · John Pignata · 2017 |
| **Chủ đề** | Serverless · Xử lý ảnh |
| **Ngày dịch** | <span style="background:#fff3c4">[dd/mm/2026]</span> |

{{% notice info %}}
Chương trình yêu cầu dịch bài gốc sang tiếng Việt. Dán bản dịch của bạn vào phần "Bản dịch" bên dưới; phần tóm tắt và liên hệ dự án là phần phân tích riêng của bạn.
{{% /notice %}}

## Tóm tắt

Bài viết trình bày cách tạo ảnh đổi kích thước theo yêu cầu. Ảnh gốc lưu trên S3; khi người dùng yêu cầu một kích thước chưa có, quy tắc chuyển hướng của S3 đưa request tới API Gateway, Lambda tải ảnh gốc, đổi kích thước, lưu lại vào S3 và chuyển hướng người dùng tới file mới. Những lần sau, ảnh được phục vụ thẳng từ S3.

## Liên hệ với dự án SnapThumb

Bài này giúp so sánh hai cách thiết kế. Bài gốc tạo ảnh **khi có người xem** (lazy), phù hợp khi cần nhiều kích thước khác nhau. SnapThumb tạo ảnh **ngay khi upload** (eager) bằng sự kiện S3, phù hợp khi chỉ cần một kích thước cố định và muốn ảnh có sẵn ngay. Phần thảo luận này dùng cho mục rủi ro và hướng phát triển của báo cáo.

## Bản dịch

<span style="background:#fff3c4">[Dán bản dịch tiếng Việt của bài viết vào đây, giữ nguyên các tiêu đề và hình ảnh của bài gốc]</span>
