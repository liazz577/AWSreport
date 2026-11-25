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

## Summary

The post shows how to let a browser or mobile app upload files straight to Amazon S3 instead of through an application server. The client calls an API Gateway endpoint, Lambda creates a presigned URL valid for 300 seconds with a random object key, and the client sends the file directly to S3. This reduces server load and network traffic; the post also notes that the bucket needs CORS rules and that a JWT authorizer should restrict who can upload.

## Relevance to SnapThumb

SnapThumb uses exactly this pattern in steps 2–5. One difference: SnapThumb uses a presigned **POST** instead of a presigned PUT, because POST supports a `content-length-range` condition so S3 itself rejects files over 10 MB, a limit the original post leaves unaddressed.

## Main content

### The problem with uploading through a server
Traditionally the user sends a file to the application server, which stores it temporarily and then moves it to storage. Every file crosses the server twice, using bandwidth and CPU. Apps with seasonal spikes (a holiday greeting app, for example) are most likely to overload exactly when they are busiest.

### The idea: separate asking for permission from sending the file
The client works in two steps:

1. Call an API Gateway endpoint. It runs the `getSignedURL` Lambda function, which asks S3 for a **presigned URL** and returns it.
2. Send the file straight to S3 with that URL. The backend never handles the file bytes.

S3 is highly available and durable, which makes it a good home for user uploads.

### The Lambda function that signs the link
- The object key is random (a random number plus `.jpg`), so users cannot overwrite each other's files.
- The content type is fixed to `image/jpeg` and the link expires after **300 seconds**. The SDK default is 15 minutes; the author suggests the shortest expiry that still works.
- The function returns JSON with the upload link and the key. Its role only needs permission to write objects (the SAM `S3WritePolicy` template).

### Deploying and testing
- The sample lives in the `aws-samples/amazon-s3-presigned-urls-aws-sam` repository and deploys with `sam deploy --guided`. The `APIendpoint` output plus `/uploads` is the address that hands out links.
- Postman test: GET to receive `uploadURL`, then PUT with the image as the body; S3 answers `200 OK` and the file appears in the bucket.
- Web test: the sample page uses `fetch` to PUT the file to the link it received.

### CORS
Because the browser sends the request to another domain (S3), the bucket needs CORS rules. The sample allows every origin and header with GET, PUT and HEAD; production should narrow this to the app's own domain.

### Security
- A second template (`templateWithAuth.yaml`) attaches a **JWT authorizer** to the HTTP API. Clients send a token in the `Authorization` header and API Gateway checks it before calling Lambda. The post uses Auth0 as the example and names Amazon Cognito as an equivalent option.
- Uploaded objects are private by default. To make them public, set the `public-read` ACL when signing, and give the function `s3:PutObjectAcl`.

### The post's conclusion
Uploading straight to S3 moves the heaviest work out of the application, so it handles traffic spikes without adding servers. The post does not cover multipart uploads or file size limits.
