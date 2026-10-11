---
title: "Create the Function"
date: 2026-09-07
weight: 1
chapter: false
pre: " <b> 5.5.1. </b> "
---

## Step 1: Create the function

1. Open the **AWS Lambda** console → **Create function**.
2. Choose **Author from scratch**.
3. **Function name:** `studymate-ask`.
4. **Runtime:** **Python 3.12**.
5. Leave **Execution role** as *Create a new role with basic Lambda permissions* → **Create function**.

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Insert screenshot:</b> Create function form → <code>static/images/5-Workshop/5.5/01-create-function.png</code></p>
<!-- ![Create function form](/images/5-Workshop/5.5/01-create-function.png?width=90pc) -->

## Step 2: Paste the code

In the **Code** tab, replace the content of `lambda_function.py` with the code below, then click **Deploy**.

```python
import json
import os
import boto3

client = boto3.client("bedrock-agent-runtime")
KB_ID = os.environ["KB_ID"]
MODEL_ARN = os.environ["MODEL_ARN"]
HEADERS = {
    "Content-Type": "application/json",
    "Access-Control-Allow-Origin": "*",
}


def reply(status, payload):
    return {"statusCode": status, "headers": HEADERS,
            "body": json.dumps(payload, ensure_ascii=False)}


def lambda_handler(event, context):
    try:
        body = json.loads(event.get("body") or "{}")
    except json.JSONDecodeError:
        return reply(400, {"error": "Body must be JSON"})

    question = (body.get("question") or "").strip()
    if not question or len(question) > 500:
        return reply(400, {"error": "Question must be 1-500 characters"})

    resp = client.retrieve_and_generate(
        input={"text": question},
        retrieveAndGenerateConfiguration={
            "type": "KNOWLEDGE_BASE",
            "knowledgeBaseConfiguration": {
                "knowledgeBaseId": KB_ID,
                "modelArn": MODEL_ARN,
            },
        },
    )

    sources = []
    for citation in resp.get("citations", []):
        for ref in citation.get("retrievedReferences", []):
            uri = ref.get("location", {}).get("s3Location", {}).get("uri")
            if uri and uri not in sources:
                sources.append(uri)

    return reply(200, {"answer": resp["output"]["text"], "sources": sources})
```

#### What the code does

| Part | Explanation |
| --- | --- |
| `boto3.client("bedrock-agent-runtime")` | Creates a client for calling the Knowledge Base. boto3 is the AWS SDK for Python and is already included in Lambda. |
| `os.environ["KB_ID"]`, `os.environ["MODEL_ARN"]` | Reads settings from environment variables so they are not hard-coded. |
| `HEADERS` | Returns JSON and allows the browser to read the response (CORS). |
| `json.loads(event.get("body"))` | API Gateway passes the request body as a string; this converts it to a dictionary. |
| Question length check | Rejects empty or very long questions to limit abuse and cost. |
| `retrieve_and_generate(...)` | One call that both retrieves relevant chunks and has Nova Lite write the answer. |
| `citations` loop | Collects the S3 paths of the source files, without duplicates. |
| `reply(200, ...)` | Returns the answer and sources as JSON. `ensure_ascii=False` keeps Vietnamese characters readable. |

## Step 3: Increase the timeout

Lambda stops a function after **3 seconds** by default, but the AI needs several seconds to write.

1. **Configuration** tab → **General configuration** → **Edit**.
2. **Timeout:** `0 min 30 sec` → **Save**.

## Step 4: Add environment variables

1. **Configuration** → **Environment variables** → **Edit** → **Add environment variable**.
2. Add the two variables below → **Save**.

| Key | Value |
| --- | --- |
| `KB_ID` | Your Knowledge Base ID from section 5.4.1 |
| `MODEL_ARN` | `arn:aws:bedrock:us-east-1::foundation-model/amazon.nova-lite-v1:0` |

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Insert screenshot:</b> environment variables KB_ID and MODEL_ARN → <code>static/images/5-Workshop/5.5/02-env-vars.png</code></p>
<!-- ![environment variables KB_ID and MODEL_ARN](/images/5-Workshop/5.5/02-env-vars.png?width=90pc) -->

{{% notice note %}}
An **ARN** (Amazon Resource Name) is the full unique address of a resource on AWS. `MODEL_ARN` tells the code exactly which model to use.
{{% /notice %}}
