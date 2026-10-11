---
title: "Dọn dẹp tài nguyên"
date: 2026-09-07
weight: 9
chapter: false
pre: " <b> 5.9. </b> "
---

Xóa tài nguyên theo thứ tự dưới đây để không còn phát sinh chi phí.

## 1. API Gateway
**API Gateway** → chọn `studymate-api` → **Delete**.

## 2. Lambda và log
1. **Lambda** → chọn `studymate-ask` → **Actions** → **Delete**.
2. **CloudWatch** → **Log groups** → chọn `/aws/lambda/studymate-ask` → **Actions** → **Delete log group(s)**.

## 3. Knowledge Base
**Amazon Bedrock** → **Knowledge Bases** → chọn `studymate-kb` → **Delete**. Nhập chữ xác nhận và đồng ý.

## 4. S3 Vectors
**Amazon S3** → **Vector buckets** → mở vector bucket do Bedrock tạo → xóa **index**, sau đó xóa **vector bucket**.

## 5. S3 bucket
Với cả `studymate-docs-<ten-ban>` và `studymate-web-<ten-ban>`: chọn bucket → **Empty** → xác nhận → rồi **Delete** → xác nhận.

## 6. IAM role
**IAM** → **Roles** → xóa các role được tạo trong workshop:
- `AmazonBedrockExecutionRoleForKnowledgeBase_...`
- `studymate-ask-role-...`

## 7. Kiểm tra hóa đơn
Một ngày sau, mở **Billing and Cost Management** → **Bills** để chắc chắn không còn chi phí phát sinh.

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Chèn ảnh:</b> danh sách tài nguyên trống sau khi dọn dẹp → <code>static/images/5-Workshop/5.9/01-cleanup.png</code></p>
<!-- ![danh sách tài nguyên trống sau khi dọn dẹp](/images/5-Workshop/5.9/01-cleanup.png?width=90pc) -->
