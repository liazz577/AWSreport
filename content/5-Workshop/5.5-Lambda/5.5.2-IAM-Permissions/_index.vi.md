---
title: "Cấp quyền IAM"
date: 2026-09-07
weight: 2
chapter: false
pre: " <b> 5.5.2. </b> "
---

## Vì sao cần bước này

Mặc định role của Lambda chỉ được ghi log vào CloudWatch, **không được phép** gọi Bedrock. Bạn thêm một inline policy cấp đúng hai quyền: truy vấn Knowledge Base của bạn và gọi mô hình Nova Lite. Chỉ cấp vừa đủ quyền cần dùng gọi là nguyên tắc **quyền tối thiểu (least privilege)**.

Bạn đã biết tạo IAM user; phần này cùng ý tưởng đó, chỉ khác là cấp quyền cho một dịch vụ thay vì cho người.

## Bước 1: Mở role

1. Trong hàm, mở **Configuration** → **Permissions**.
2. Bấm vào tên role ở mục **Execution role** (ví dụ `studymate-ask-role-xxxx`). Console IAM sẽ mở ra.

## Bước 2: Thêm inline policy

1. **Add permissions** → **Create inline policy**.
2. Chuyển sang tab **JSON** và dán policy dưới đây.
3. Thay `ACCOUNT_ID` bằng account ID 12 chữ số của bạn (xem ở menu góc trên bên phải console) và `KB_ID` bằng Knowledge Base ID.
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

| Statement | Ý nghĩa |
| --- | --- |
| `QueryKnowledgeBase` | **Chỉ** cho phép truy vấn Knowledge Base `studymate-kb` |
| `InvokeNovaLite` | **Chỉ** cho phép gọi mô hình Nova Lite |

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Chèn ảnh:</b> inline policy trong trình soạn JSON → <code>static/images/5-Workshop/5.5/03-inline-policy.png</code></p>
<!-- ![inline policy trong trình soạn JSON](/images/5-Workshop/5.5/03-inline-policy.png?width=90pc) -->

## Bước 3: Kiểm thử hàm

1. Quay lại Lambda, mở tab **Test** → **Create new event**.
2. **Event name:** `ask-scholarship`. Dán nội dung event sau:

```json
{
  "body": "{\"question\": \"Điều kiện để được xét học bổng là gì?\"}"
}
```

3. Bấm **Test**. Kết quả phải có `"statusCode": 200` và `body` chứa `answer` và `sources`.

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Chèn ảnh:</b> kết quả test thành công với statusCode 200 → <code>static/images/5-Workshop/5.5/04-test-result.png</code></p>
<!-- ![kết quả test thành công với statusCode 200](/images/5-Workshop/5.5/04-test-result.png?width=90pc) -->

{{% notice warning %}}
Lỗi `AccessDeniedException` nghĩa là policy sai: kiểm tra lại `ACCOUNT_ID`, `KB_ID` và region `us-east-1`. Lỗi "Task timed out after 3.00 seconds" nghĩa là bạn chưa tăng timeout.
{{% /notice %}}
