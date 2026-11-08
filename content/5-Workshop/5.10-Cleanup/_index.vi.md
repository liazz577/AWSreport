---
title: "Dọn dẹp tài nguyên"
date: 2026-09-07
weight: 10
chapter: false
pre: " <b> 5.10. </b> "
---

## Nếu bạn dựng bằng tay (5.3–5.7)
1. **API Gateway:** xoá `snapthumb-api`.
2. **Lambda:** xoá `snapthumb-api` và `snapthumb-thumbnail`; **Layers:** xoá `pillow-py312`.
3. **CloudWatch → Log groups:** xoá `/aws/lambda/snapthumb-api` và `/aws/lambda/snapthumb-thumbnail`.
4. **DynamoDB:** xoá `snapthumb-images`.
5. **S3:** **Empty** rồi **Delete** `snapthumb-images-…` và `snapthumb-web-…`.
6. **IAM → Roles:** xoá các role bắt đầu bằng `snapthumb-api-role` và `snapthumb-thumbnail-role`.

## Nếu bạn dùng CloudFormation (5.9)

```bash
cd ~/snapthumb && bash destroy.sh
```

Gõ `yes`; script làm trống hai bucket rồi xoá stack.

## Kiểm tra hoá đơn
Một ngày sau, mở **Billing and Cost Management → Bills** để chắc chắn không còn chi phí phát sinh.
