---
title: "Configure CORS and Test"
date: 2026-09-07
weight: 2
chapter: false
pre: " <b> 5.6.2. </b> "
---

## What is CORS?

Browsers have a safety rule: a page served from address A may not call address B unless B explicitly allows it. The web page lives on S3 and the API lives on API Gateway, which are different addresses, so you must enable **CORS** (Cross-Origin Resource Sharing) on the API.

## Configure CORS

1. In `studymate-api`, choose **CORS** in the left menu → **Configure**.
2. Fill in:

| Field | Value |
| --- | --- |
| Access-Control-Allow-Origin | `*` |
| Access-Control-Allow-Methods | `POST` |
| Access-Control-Allow-Headers | `content-type` |

3. **Save**.

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Insert screenshot:</b> CORS configuration → <code>static/images/5-Workshop/5.6/03-cors.png</code></p>
<!-- ![CORS configuration](/images/5-Workshop/5.6/03-cors.png?width=90pc) -->

{{% notice tip %}}
`*` allows any website to call the API, which is fine for a demo. In production, replace it with your website's address.
{{% /notice %}}

## Test with curl

Run this in a terminal (replace the URL). This tests the API without the web page, so if it fails you know the problem is in the API or Lambda, not the page.

```bash
curl -X POST "https://abc123.execute-api.us-east-1.amazonaws.com/ask" \
  -H "content-type: application/json" \
  -d '{"question": "Thủ tục bảo lưu kết quả học tập như thế nào?"}'
```

Expected output:

```json
{"answer": "...", "sources": ["s3://studymate-docs-.../quy-che-dao-tao.pdf"]}
```

## Add throttling (optional)

To prevent abuse, open **Throttling** in the left menu → select the `$default` stage → **Edit** and set, for example, a **rate limit** of 5 and a **burst limit** of 10 requests.
