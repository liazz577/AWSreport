---
title: "Tạo hàm"
date: 2026-09-07
weight: 1
chapter: false
pre: " <b> 5.5.1. </b> "
---

## Bước 1: Tạo hàm

1. Mở console **AWS Lambda** → **Create function**.
2. Chọn **Author from scratch**.
3. **Function name:** `studymate-ask`.
4. **Runtime:** **Python 3.12**.
5. Phần **Execution role** để mặc định *Create a new role with basic Lambda permissions* → **Create function**.

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Chèn ảnh:</b> form Create function → <code>static/images/5-Workshop/5.5/01-create-function.png</code></p>
<!-- ![form Create function](/images/5-Workshop/5.5/01-create-function.png?width=90pc) -->

## Bước 2: Dán code

Trong tab **Code**, thay toàn bộ nội dung file `lambda_function.py` bằng đoạn code dưới đây, rồi bấm **Deploy**.

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

#### Giải thích code

| Phần code | Giải thích |
| --- | --- |
| `boto3.client("bedrock-agent-runtime")` | Tạo client để gọi Knowledge Base. boto3 là thư viện AWS cho Python, có sẵn trong Lambda. |
| `os.environ["KB_ID"]`, `os.environ["MODEL_ARN"]` | Đọc cấu hình từ biến môi trường thay vì ghi cứng vào code. |
| `HEADERS` | Trả về dạng JSON và cho phép trình duyệt đọc kết quả (CORS). |
| `json.loads(event.get("body"))` | API Gateway gửi nội dung request dưới dạng chuỗi; dòng này đổi nó thành dictionary. |
| Kiểm tra độ dài câu hỏi | Từ chối câu hỏi rỗng hoặc quá dài để hạn chế lạm dụng và chi phí. |
| `retrieve_and_generate(...)` | Một lệnh duy nhất vừa tìm đoạn tài liệu liên quan vừa để Nova Lite viết câu trả lời. |
| Vòng lặp `citations` | Gom đường dẫn S3 của các file nguồn, bỏ trùng lặp. |
| `reply(200, ...)` | Trả câu trả lời và nguồn dưới dạng JSON. `ensure_ascii=False` giữ tiếng Việt có dấu đọc được. |

## Bước 3: Tăng timeout

Mặc định Lambda dừng hàm sau **3 giây**, mà AI cần vài giây để viết câu trả lời.

1. Tab **Configuration** → **General configuration** → **Edit**.
2. **Timeout:** `0 min 30 sec` → **Save**.

## Bước 4: Thêm biến môi trường

1. **Configuration** → **Environment variables** → **Edit** → **Add environment variable**.
2. Thêm hai biến dưới đây → **Save**.

| Key | Value |
| --- | --- |
| `KB_ID` | Knowledge Base ID ở mục 5.4.1 |
| `MODEL_ARN` | `arn:aws:bedrock:us-east-1::foundation-model/amazon.nova-lite-v1:0` |

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Chèn ảnh:</b> biến môi trường KB_ID và MODEL_ARN → <code>static/images/5-Workshop/5.5/02-env-vars.png</code></p>
<!-- ![biến môi trường KB_ID và MODEL_ARN](/images/5-Workshop/5.5/02-env-vars.png?width=90pc) -->

{{% notice note %}}
**ARN** (Amazon Resource Name) là địa chỉ đầy đủ, duy nhất của một tài nguyên trên AWS. `MODEL_ARN` cho code biết chính xác dùng mô hình nào.
{{% /notice %}}
