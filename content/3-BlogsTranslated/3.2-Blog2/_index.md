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

## Summary

The post shows how to resize images on demand. Originals are stored in S3; when a user requests a size that does not exist yet, an S3 redirect rule sends the request to API Gateway, Lambda downloads the original, resizes it, saves it to S3 and redirects the user to the new file. Later requests are served straight from S3.

## Relevance to SnapThumb

This post helps compare two designs. The original creates images **when someone views them** (lazy), which suits many different sizes. SnapThumb creates them **on upload** (eager) through S3 events, which suits one fixed size that should be ready immediately. This comparison feeds the risk and future-work parts of the report.

## Main content

### Eager or lazy processing
The common approach is **eager processing**: whenever a new image arrives, an S3 event calls Lambda to create every size up front. The post proposes **lazy processing**: a size is created only the first time someone asks for it. The author gives three benefits:

- **Faster design changes:** adding a new size does not require reprocessing the whole image archive.
- **Lower storage cost:** sizes nobody uses are never created; lifecycle rules can expire old resized files.
- **Better resilience:** if one resize fails, the next request retries it instead of the image staying broken for everyone.

### How one request flows
1. The browser asks the bucket's website endpoint for a resized image.
2. The image does not exist yet, so S3 sends a temporary redirect to the API.
3. The browser follows the redirect to API Gateway.
4. API Gateway invokes the Lambda function.
5. Lambda downloads the original, resizes it and saves it to the bucket under the requested key.
6. API Gateway permanently redirects the browser to the new file in S3.
7. From then on S3 serves the image directly. If the file is deleted, the cycle repeats.

### The S3 redirect rule
The bucket has static website hosting and a routing rule: on a 404 error, redirect (code **307**, temporary) to the API hostname, turning the path into the `key` parameter of the resize function. A 307 keeps browsers from remembering the redirect, because once the image exists S3 can serve it.

### Setup steps
- **Bucket:** allow public read, enable static website hosting, note the endpoint.
- **Lambda:** a function named `resize` with an open API Gateway trigger and code from `function.zip` in the `serverless-image-resizing` repository. Memory **1536 MB**, timeout **10 seconds**. The role only needs CloudWatch Logs and `s3:PutObject` on the bucket.
- **Environment variables:** `BUCKET` (bucket name) and `URL` (the bucket's website endpoint).
- **Testing:** upload the sample image, then open sizes such as 300x300, 25x25 and 500x500; check CloudWatch Logs if something fails.

### Notes for production
- The post's version is a simplified example. Production needs an **allowed-sizes list**, otherwise anyone can request endless sizes and fill the bucket.
- Keep resized images in a separate bucket so lifecycle rules can apply to them.
- A 2018 update recommends the AWS **Serverless Image Handler** solution for production.
- General serverless benefits: no servers to patch or scale, and no capacity to over-provision.
