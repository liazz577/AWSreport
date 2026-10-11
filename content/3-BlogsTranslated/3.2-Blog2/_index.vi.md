---
title: "Blog 2"
date: 2026-09-07
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

# Simplified model access in Amazon Bedrock

| Mục | Chi tiết |
| --- | --- |
| **Bài gốc** | [Simplified model access in Amazon Bedrock](https://aws.amazon.com/blogs/security/simplified-amazon-bedrock-model-access/) |
| **Nguồn** | AWS Security Blog |
| **Chủ đề** | Bảo mật · Quản lý quyền truy cập |
| **Ngày dịch** | <span style="background:#fff3c4">[dd/mm/2026]</span> |

{{% notice info %}}
Chương trình yêu cầu dịch bài gốc sang tiếng Việt. Dán bản dịch của bạn vào phần "Bản dịch" bên dưới; phần tóm tắt và liên hệ dự án là phần phân tích riêng của bạn.
{{% /notice %}}

## Tóm tắt

Amazon Bedrock đã bỏ trang "Model access": các mô hình serverless được bật sẵn trong mọi region thương mại, và quyền truy cập được kiểm soát bằng IAM policy và Service Control Policy như các dịch vụ AWS khác. Riêng mô hình của Anthropic vẫn yêu cầu điền form mô tả mục đích sử dụng một lần.

## Liên hệ với dự án StudyMate

Vì mô hình được bật sẵn, việc giới hạn quyền trở nên quan trọng hơn. Trong StudyMate, IAM policy của Lambda chỉ cho phép gọi đúng mô hình Nova Lite và đúng Knowledge Base của dự án (mục 5.5.2).

## Bản dịch

<span style="background:#fff3c4">[Dán bản dịch tiếng Việt của bài viết vào đây, giữ nguyên các tiêu đề và hình ảnh của bài gốc]</span>
