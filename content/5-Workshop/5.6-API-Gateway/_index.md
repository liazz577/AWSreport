---
title: "Create the API with API Gateway"
date: 2026-09-07
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

## Why API Gateway?

A Lambda function has no web address of its own. **Amazon API Gateway** gives it a public HTTPS URL so the web page can send questions to it. `POST /ask` means "send data to the `/ask` path".

#### Content

1. [Create the HTTP API](5.6.1-create-http-api/)
2. [Configure CORS and test](5.6.2-configure-cors/)
