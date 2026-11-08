---
title: "Clean Up Resources"
date: 2026-09-07
weight: 10
chapter: false
pre: " <b> 5.10. </b> "
---

## If you built by hand (5.3–5.7)
1. **API Gateway:** delete `snapthumb-api`.
2. **Lambda:** delete `snapthumb-api` and `snapthumb-thumbnail`; **Layers:** delete `pillow-py312`.
3. **CloudWatch → Log groups:** delete `/aws/lambda/snapthumb-api` and `/aws/lambda/snapthumb-thumbnail`.
4. **DynamoDB:** delete `snapthumb-images`.
5. **S3:** **Empty** then **Delete** `snapthumb-images-…` and `snapthumb-web-…`.
6. **IAM → Roles:** delete the roles starting with `snapthumb-api-role` and `snapthumb-thumbnail-role`.

## If you used CloudFormation (5.9)

```bash
cd ~/snapthumb && bash destroy.sh
```

Type `yes`; the script empties both buckets and deletes the stack.

## Check billing
A day later, open **Billing and Cost Management → Bills** to confirm nothing is still accruing.
