---
title: "Create the API"
date: 2026-09-07
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

The web page needs three operations, all handled by one API Lambda:

| Route | Purpose |
| --- | --- |
| `POST /uploads` | Validate the file, write a `PENDING` item, return a presigned POST |
| `GET /images` | List the newest 50 images with view links valid for 1 hour |
| `DELETE /images/{id}` | Delete the original, the thumbnail and the item |

#### Content
1. [Create the API Lambda](5.6.1-api-lambda/)
2. [Create the HTTP API](5.6.2-http-api/)
