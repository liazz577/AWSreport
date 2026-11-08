---
title: "Tạo HTTP API"
date: 2026-09-07
weight: 2
chapter: false
pre: " <b> 5.6.2. </b> "
---

## Bước 1: Tạo API
1. **API Gateway → Create API → HTTP API → Build**.
2. **Add integration → Lambda** → `snapthumb-api`. **API name:** `snapthumb-api` → **Next**.
3. **Configure routes:** thêm ba route, đều trỏ tới `snapthumb-api`:
   - `POST` `/uploads`
   - `GET` `/images`
   - `DELETE` `/images/{id}`
4. Stage `$default` bật **Auto-deploy** → **Next → Create**.

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Chèn ảnh:</b> ba route của API → <code>static/images/5-Workshop/5.6/01-routes.png</code></p>
<!-- ![ba route của API](/images/5-Workshop/5.6/01-routes.png?width=90pc) -->

## Bước 2: CORS
**CORS → Configure:** Allow origin `*`, Allow methods `GET, POST, DELETE`, Allow headers `content-type` → **Save**.

## Bước 3: Giới hạn tần suất
**Throttling → Edit** stage `$default`: rate `10`, burst `20`.

## Bước 4: Thử bằng curl
Sao chép **Invoke URL** rồi chạy trong CloudShell:

```bash
API=https://abc123.execute-api.ap-southeast-1.amazonaws.com
curl -s "$API/images"; echo
curl -s -X POST "$API/uploads" -H "content-type: application/json" \
  -d '{"filename":"test.jpg","contentType":"image/jpeg","size":12345}'; echo
```

Lệnh đầu trả về `{"images": [...]}`; lệnh sau trả về `id` và đối tượng `upload` gồm `url` và `fields`.
