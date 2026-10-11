---
title: "Prerequisites"
date: 2026-09-07
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

## 1. AWS account and IAM user

You need an AWS account and an **IAM user with administrator permissions** for this lab. Do not use the root user for daily work.

## 2. Select the region

At the top right of the console, select **US East (N. Virginia) us-east-1** and keep it for the whole workshop.

{{% notice note %}}
A region is a group of AWS data centers in one area. Resources created in one region do not show up in another. If you ever "lose" a resource, check the region first.
{{% /notice %}}

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Insert screenshot:</b> region selector set to us-east-1 → <code>static/images/5-Workshop/5.2/01-region.png</code></p>
<!-- ![region selector set to us-east-1](/images/5-Workshop/5.2/01-region.png?width=90pc) -->

## 3. Create a cost alert

1. Open **Billing and Cost Management → Budgets → Create budget**.
2. Choose **Use a template** → **Monthly cost budget**.
3. Budget amount: **5** USD. Enter your email address.
4. Click **Create budget**.

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Insert screenshot:</b> the created budget → <code>static/images/5-Workshop/5.2/02-budget.png</code></p>
<!-- ![the created budget](/images/5-Workshop/5.2/02-budget.png?width=90pc) -->

## 4. Prepare the documents

Prepare 2–3 **text-based** PDFs, for example your university's student handbook and academic regulations.

{{% notice warning %}}
Quick check: open the PDF and try to select a line of text. If you cannot, it is a scanned image and the Knowledge Base will not be able to read it.
{{% /notice %}}

## 5. Bedrock model access

Serverless models on Amazon Bedrock are now **enabled by default**; there is no "Model access" page to visit. Anthropic (Claude) models still require a one-time use-case form, but this workshop uses **Amazon Nova Lite**, so you can skip that.
