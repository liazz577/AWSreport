---
title: "Create the DynamoDB Table"
date: 2026-09-07
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

## Create the table
1. **Amazon DynamoDB → Tables → Create table**.
2. **Table name:** `snapthumb-images`.
3. **Partition key:** `id`, type **String**.
4. **Table settings:** **Customize settings** → **Capacity mode: On-demand**.
5. **Create table**.

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Insert screenshot:</b> table snapthumb-images with status Active → <code>static/images/5-Workshop/5.4/01-table.png</code></p>
<!-- ![table snapthumb-images with status Active](/images/5-Workshop/5.4/01-table.png?width=90pc) -->

## What one item looks like

| Attribute | Example | Written by |
| --- | --- | --- |
| `id` | `3f9c…` | API Lambda |
| `filename`, `contentType`, `originalKey`, `createdAt` | `anh-lop.jpg`, `image/jpeg`, `uploads/3f9c….jpg` | API Lambda |
| `status` | `PENDING` → `DONE` or `FAILED` | API Lambda, then Thumbnail Lambda |
| `thumbKey`, `originalWidth/Height`, `thumbWidth/Height`, `originalBytes`, `thumbBytes`, `processingMs` | `thumbnails/3f9c….jpg`, 4000×3000, 320×240 … | Thumbnail Lambda |

DynamoDB has no fixed schema: only `id` is declared; the other attributes are added when written.
