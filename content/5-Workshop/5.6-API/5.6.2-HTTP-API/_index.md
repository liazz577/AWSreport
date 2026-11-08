---
title: "Create the HTTP API"
date: 2026-09-07
weight: 2
chapter: false
pre: " <b> 5.6.2. </b> "
---

## Step 1: Create the API
1. **API Gateway → Create API → HTTP API → Build**.
2. **Add integration → Lambda** → `snapthumb-api`. **API name:** `snapthumb-api` → **Next**.
3. **Configure routes:** add three routes, each with target `snapthumb-api`:
   - `POST` `/uploads`
   - `GET` `/images`
   - `DELETE` `/images/{id}`
4. Stage `$default` with **Auto-deploy** → **Next → Create**.

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Insert screenshot:</b> the three routes → <code>static/images/5-Workshop/5.6/01-routes.png</code></p>
<!-- ![the three routes](/images/5-Workshop/5.6/01-routes.png?width=90pc) -->

## Step 2: CORS
**CORS → Configure:** Allow origin `*`, Allow methods `GET, POST, DELETE`, Allow headers `content-type` → **Save**.

## Step 3: Throttling
**Throttling → Edit** the `$default` stage: rate `10`, burst `20`.

## Step 4: Test with curl
Copy the **Invoke URL** and run in CloudShell:

```bash
API=https://abc123.execute-api.ap-southeast-1.amazonaws.com
curl -s "$API/images"; echo
curl -s -X POST "$API/uploads" -H "content-type: application/json" \
  -d '{"filename":"test.jpg","contentType":"image/jpeg","size":12345}'; echo
```

The first call returns `{"images": [...]}`; the second returns an `id` and an `upload` object with `url` and `fields`.
