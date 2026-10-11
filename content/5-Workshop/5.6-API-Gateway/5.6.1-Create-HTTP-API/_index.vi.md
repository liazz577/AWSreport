---
title: "Tạo HTTP API"
date: 2026-09-07
weight: 1
chapter: false
pre: " <b> 5.6.1. </b> "
---

## Bước 1: Tạo API

1. Mở console **API Gateway** → **Create API**.
2. Ở mục **HTTP API**, bấm **Build**.
3. **Integrations:** **Add integration** → **Lambda** → chọn `studymate-ask`.
4. **API name:** `studymate-api` → **Next**.

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Chèn ảnh:</b> HTTP API với integration là hàm Lambda → <code>static/images/5-Workshop/5.6/01-integration.png</code></p>
<!-- ![HTTP API với integration là hàm Lambda](/images/5-Workshop/5.6/01-integration.png?width=90pc) -->

## Bước 2: Cấu hình route

1. **Method:** `POST`.
2. **Resource path:** `/ask`.
3. **Integration target:** `studymate-ask` → **Next**.

## Bước 3: Stage

1. Giữ stage `$default` với **Auto-deploy** đang bật → **Next** → **Create**.

## Bước 4: Ghi lại Invoke URL

Trên trang API, sao chép **Invoke URL**, có dạng `https://abc123.execute-api.us-east-1.amazonaws.com`. Địa chỉ đầy đủ để gọi là URL đó cộng thêm `/ask`.

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Chèn ảnh:</b> trang chi tiết API có Invoke URL → <code>static/images/5-Workshop/5.6/02-invoke-url.png</code></p>
<!-- ![trang chi tiết API có Invoke URL](/images/5-Workshop/5.6/02-invoke-url.png?width=90pc) -->

{{% notice note %}}
API Gateway tự thêm quyền để API được gọi hàm Lambda, bạn không cần sửa IAM ở bước này.
{{% /notice %}}
