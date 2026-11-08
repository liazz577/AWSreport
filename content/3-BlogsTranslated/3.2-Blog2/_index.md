---
title: "Blog 2"
date: 2026-09-07
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

# Resize Images on the Fly with Amazon S3, AWS Lambda, and Amazon API Gateway

| Item | Detail |
| --- | --- |
| **Original post** | [Resize Images on the Fly with Amazon S3, AWS Lambda, and Amazon API Gateway](https://aws.amazon.com/blogs/compute/resize-images-on-the-fly-with-amazon-s3-aws-lambda-and-amazon-api-gateway/) |
| **Source** | AWS Compute Blog · John Pignata · 2017 |
| **Topic** | Serverless · Image processing |
| **Translated on** | <span style="background:#fff3c4">[dd/mm/2026]</span> |

## Summary

The post shows how to resize images on demand. Originals are stored in S3; when a user requests a size that does not exist yet, an S3 redirect rule sends the request to API Gateway, Lambda downloads the original, resizes it, saves it to S3 and redirects the user to the new file. Later requests are served straight from S3.

## Relevance to SnapThumb

This post helps compare two designs. The original creates images **when someone views them** (lazy), which suits many different sizes. SnapThumb creates them **on upload** (eager) through S3 events, which suits one fixed size that should be ready immediately. This comparison feeds the risk and future-work parts of the report.

## Translation

<span style="background:#fff3c4">[The Vietnamese translation lives on the Vietnamese version of this page]</span>
