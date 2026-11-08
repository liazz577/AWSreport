---
title: "Blog 1"
date: 2026-09-07
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# Uploading to Amazon S3 directly from a web or mobile application

| Item | Detail |
| --- | --- |
| **Original post** | [Uploading to Amazon S3 directly from a web or mobile application](https://aws.amazon.com/blogs/compute/uploading-to-amazon-s3-directly-from-a-web-or-mobile-application/) |
| **Source** | AWS Compute Blog · James Beswick · 2020 |
| **Topic** | Serverless · Storage |
| **Translated on** | <span style="background:#fff3c4">[dd/mm/2026]</span> |

## Summary

The post shows how to let a browser or mobile app upload files straight to Amazon S3 instead of through an application server. The client calls an API Gateway endpoint, Lambda creates a presigned URL valid for 300 seconds with a random object key, and the client sends the file directly to S3. This reduces server load and network traffic; the post also notes that the bucket needs CORS rules and that a JWT authorizer should restrict who can upload.

## Relevance to SnapThumb

SnapThumb uses exactly this pattern in steps 2–5. One difference: SnapThumb uses a presigned **POST** instead of a presigned PUT, because POST supports a `content-length-range` condition so S3 itself rejects files over 10 MB, a limit the original post leaves unaddressed.

## Translation

<span style="background:#fff3c4">[The Vietnamese translation lives on the Vietnamese version of this page]</span>
