---
title: "Workshop Overview"
date: 2026-09-07
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

## How SnapThumb works

The system has two Lambda functions with different jobs:

| Component | AWS service | What it does |
| --- | --- | --- |
| Web page | **Amazon S3** (static website) | Drag-and-drop upload and a gallery of thumbnails |
| Front door | **Amazon API Gateway** | HTTPS endpoints the page calls |
| API Lambda | **AWS Lambda** | Validates files, signs time-limited upload and view links, lists and deletes images |
| Image storage | **Amazon S3** (private bucket) | Originals under `uploads/`, thumbnails under `thumbnails/` |
| Thumbnail Lambda | **AWS Lambda** + Pillow | Runs automatically for each new image and creates its thumbnail |
| Image records | **Amazon DynamoDB** | Status (`PENDING` → `DONE` / `FAILED`), dimensions, sizes, processing time |
| Logs | **Amazon CloudWatch** | Every run of both functions |

## Key concepts

#### Event-driven architecture
Nothing polls the bucket. When an object is created, S3 emits an **event** and invokes the Lambda function directly. The function runs only when there is work, and AWS runs many copies in parallel when many images arrive.

#### S3 Event Notifications
A bucket setting that says "when an object is created under this prefix, call this target". SnapThumb listens only to `uploads/`. Thumbnails are written to `thumbnails/`, so writing a thumbnail never triggers the function again. Without this split, the function would trigger itself in an endless loop.

#### Presigned URL / presigned POST
A link signed with the API Lambda's permissions that lets the browser upload one specific object directly to S3 for a short time (5 minutes). The browser never gets AWS credentials, and large files never pass through Lambda. The presigned POST also carries conditions: S3 rejects files that are not the declared type or are larger than 10 MB.

#### Lambda layer
A zip of libraries shared by functions. Pillow (the Python imaging library) is not included in the Lambda runtime, so you publish it once as a layer and attach it to the Thumbnail function.

#### DynamoDB on-demand
A NoSQL table billed per request, with no capacity to plan. Each image is one item keyed by `id`.

{{% notice tip %}}
Test each part before moving on: upload a file to the bucket by hand and check that a thumbnail appears (5.5) → call the API with curl (5.6) → use the web page (5.7).
{{% /notice %}}
