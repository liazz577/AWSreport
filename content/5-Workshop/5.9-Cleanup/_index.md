---
title: "Clean Up Resources"
date: 2026-09-07
weight: 9
chapter: false
pre: " <b> 5.9. </b> "
---

Delete resources in this order so nothing keeps charging you.

## 1. API Gateway
**API Gateway** → select `studymate-api` → **Delete**.

## 2. Lambda and logs
1. **Lambda** → select `studymate-ask` → **Actions** → **Delete**.
2. **CloudWatch** → **Log groups** → select `/aws/lambda/studymate-ask` → **Actions** → **Delete log group(s)**.

## 3. Knowledge Base
**Amazon Bedrock** → **Knowledge Bases** → select `studymate-kb` → **Delete**. Type the confirmation text and confirm.

## 4. S3 Vectors
**Amazon S3** → **Vector buckets** → open the vector bucket created by Bedrock → delete its **index**, then delete the **vector bucket**.

## 5. S3 buckets
For both `studymate-docs-<your-name>` and `studymate-web-<your-name>`: select the bucket → **Empty** → confirm → then **Delete** → confirm.

## 6. IAM roles
**IAM** → **Roles** → delete the roles created during the workshop:
- `AmazonBedrockExecutionRoleForKnowledgeBase_...`
- `studymate-ask-role-...`

## 7. Check billing
A day later, open **Billing and Cost Management** → **Bills** to confirm no charges are still accruing.

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Insert screenshot:</b> empty resource lists after cleanup → <code>static/images/5-Workshop/5.9/01-cleanup.png</code></p>
<!-- ![empty resource lists after cleanup](/images/5-Workshop/5.9/01-cleanup.png?width=90pc) -->
