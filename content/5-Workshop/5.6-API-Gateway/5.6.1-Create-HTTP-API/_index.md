---
title: "Create the HTTP API"
date: 2026-09-07
weight: 1
chapter: false
pre: " <b> 5.6.1. </b> "
---

## Step 1: Create the API

1. Open the **API Gateway** console → **Create API**.
2. Under **HTTP API**, click **Build**.
3. **Integrations:** **Add integration** → **Lambda** → choose `studymate-ask`.
4. **API name:** `studymate-api` → **Next**.

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Insert screenshot:</b> HTTP API with the Lambda integration → <code>static/images/5-Workshop/5.6/01-integration.png</code></p>
<!-- ![HTTP API with the Lambda integration](/images/5-Workshop/5.6/01-integration.png?width=90pc) -->

## Step 2: Configure the route

1. **Method:** `POST`.
2. **Resource path:** `/ask`.
3. **Integration target:** `studymate-ask` → **Next**.

## Step 3: Stage

1. Keep the `$default` stage with **Auto-deploy** on → **Next** → **Create**.

## Step 4: Note the Invoke URL

On the API page, copy the **Invoke URL**, which looks like `https://abc123.execute-api.us-east-1.amazonaws.com`. Your full endpoint is that URL plus `/ask`.

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Insert screenshot:</b> API details with the Invoke URL → <code>static/images/5-Workshop/5.6/02-invoke-url.png</code></p>
<!-- ![API details with the Invoke URL](/images/5-Workshop/5.6/02-invoke-url.png?width=90pc) -->

{{% notice note %}}
API Gateway automatically adds permission for the API to invoke your Lambda function, so you do not need to edit IAM here.
{{% /notice %}}
