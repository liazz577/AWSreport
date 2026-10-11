---
title: "Grant IAM Permissions"
date: 2026-09-07
weight: 2
chapter: false
pre: " <b> 5.5.2. </b> "
---

## Why this step is needed

By default the Lambda role can only write logs to CloudWatch. It is **not allowed** to call Bedrock. You add an inline policy that grants exactly two permissions: query your Knowledge Base, and invoke Nova Lite. Granting only what is needed is called **least privilege**.

## Step 1: Open the role

1. In the function, open **Configuration** → **Permissions**.
2. Click the role name under **Execution role** (e.g. `studymate-ask-role-xxxx`). The IAM console opens.

## Step 2: Add an inline policy

1. **Add permissions** → **Create inline policy**.
2. Switch to the **JSON** tab and paste the policy below.
3. Replace `ACCOUNT_ID` with your 12-digit account ID (top right menu of the console) and `KB_ID` with your Knowledge Base ID.
4. **Next** → **Policy name:** `studymate-bedrock-access` → **Create policy**.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "QueryKnowledgeBase",
      "Effect": "Allow",
      "Action": ["bedrock:RetrieveAndGenerate", "bedrock:Retrieve"],
      "Resource": "arn:aws:bedrock:us-east-1:ACCOUNT_ID:knowledge-base/KB_ID"
    },
    {
      "Sid": "InvokeNovaLite",
      "Effect": "Allow",
      "Action": ["bedrock:InvokeModel"],
      "Resource": "arn:aws:bedrock:us-east-1::foundation-model/amazon.nova-lite-v1:0"
    }
  ]
}
```

| Statement | Meaning |
| --- | --- |
| `QueryKnowledgeBase` | Allows querying **only** the `studymate-kb` Knowledge Base |
| `InvokeNovaLite` | Allows invoking **only** the Nova Lite model |

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Insert screenshot:</b> inline policy in the JSON editor → <code>static/images/5-Workshop/5.5/03-inline-policy.png</code></p>
<!-- ![inline policy in the JSON editor](/images/5-Workshop/5.5/03-inline-policy.png?width=90pc) -->

## Step 3: Test the function

1. Back in Lambda, open the **Test** tab → **Create new event**.
2. **Event name:** `ask-scholarship`. Paste this event JSON:

```json
{
  "body": "{\"question\": \"Điều kiện để được xét học bổng là gì?\"}"
}
```

3. Click **Test**. The result should show `"statusCode": 200` and a `body` containing `answer` and `sources`.

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Insert screenshot:</b> successful test result with statusCode 200 → <code>static/images/5-Workshop/5.5/04-test-result.png</code></p>
<!-- ![successful test result with statusCode 200](/images/5-Workshop/5.5/04-test-result.png?width=90pc) -->

{{% notice warning %}}
`AccessDeniedException` means the policy is wrong: check `ACCOUNT_ID`, `KB_ID` and that you are in `us-east-1`. "Task timed out after 3.00 seconds" means you skipped the timeout step.
{{% /notice %}}
