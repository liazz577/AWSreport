---
title: "Cấu hình CORS và kiểm thử"
date: 2026-09-07
weight: 2
chapter: false
pre: " <b> 5.6.2. </b> "
---

## CORS là gì?

Trình duyệt có một luật an toàn: trang web ở địa chỉ A không được tự ý gọi tới địa chỉ B, trừ khi B cho phép. Trang web nằm trên S3 còn API nằm ở API Gateway, tức là hai địa chỉ khác nhau, nên bạn phải bật **CORS** (Cross-Origin Resource Sharing) cho API.

## Cấu hình CORS

1. Trong `studymate-api`, chọn **CORS** ở menu bên trái → **Configure**.
2. Điền:

| Trường | Giá trị |
| --- | --- |
| Access-Control-Allow-Origin | `*` |
| Access-Control-Allow-Methods | `POST` |
| Access-Control-Allow-Headers | `content-type` |

3. **Save**.

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Chèn ảnh:</b> cấu hình CORS → <code>static/images/5-Workshop/5.6/03-cors.png</code></p>
<!-- ![cấu hình CORS](/images/5-Workshop/5.6/03-cors.png?width=90pc) -->

{{% notice tip %}}
`*` cho phép mọi trang web gọi API, chấp nhận được khi demo. Khi chạy thật, hãy thay bằng địa chỉ trang web của bạn.
{{% /notice %}}

## Kiểm thử bằng curl

Chạy lệnh sau trong terminal (thay URL của bạn). Lệnh này thử API mà không cần trang web, nên nếu lỗi thì bạn biết vấn đề nằm ở API hoặc Lambda chứ không phải trang web.

```bash
curl -X POST "https://abc123.execute-api.us-east-1.amazonaws.com/ask" \
  -H "content-type: application/json" \
  -d '{"question": "Thủ tục bảo lưu kết quả học tập như thế nào?"}'
```

Kết quả mong đợi:

```json
{"answer": "...", "sources": ["s3://studymate-docs-.../quy-che-dao-tao.pdf"]}
```

## Giới hạn tần suất gọi (không bắt buộc)

Để tránh bị lạm dụng, mở **Throttling** ở menu bên trái → chọn stage `$default` → **Edit** và đặt, ví dụ, **rate limit** là 5 và **burst limit** là 10 request.
