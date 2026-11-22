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
7. **CloudWatch → Alarms:** delete the three `snapthumb-…` alarms; **Amazon SNS:** delete the topic `snapthumb-alarms` and its subscription. (The metric filter is deleted with its log group.)

## If you used CloudFormation (5.9)

```bash
cd ~/snapthumb && bash destroy.sh
```

Type `yes`; the script empties both buckets and deletes the stack. Deleting the stack also removes the three alarms, the SNS topic and its subscription, the metric filter, both Lambda functions, the API, the table, the IAM roles and the log groups.

If you also published the Pillow layer in 5.5.1, delete it as well: **Lambda → Layers → `pillow-py312` → Delete** for each version. Layers are not part of the stack.

## Cost of leaving it running
If you need to keep SnapThumb online for a while (for example until the report is graded), it costs almost nothing (the detailed estimate by usage is in [Proposal, section 6](../../2-proposal/)):

| Part | Cost while idle | Note |
| --- | --- | --- |
| Lambda, API Gateway, DynamoDB on-demand | 0 USD | Billed per request only |
| S3 | About 0.025 USD per GB per month | A few test images are a few MB |
| CloudWatch | 0 USD | 3 alarms and 1 custom metric fit in the Free Tier (10 alarms, 10 custom metrics); logs are kept 14 days |
| SNS email | 0 USD | Emails are sent only when an alarm changes state |

Delete everything as soon as you no longer need it.

## Check billing
A day later, open **Billing and Cost Management → Bills** to confirm nothing is still accruing.
