---
title: "Workshop"
date: 2026-09-07
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Building StudyMate: a Student Handbook Q&A Chatbot with Amazon Bedrock

#### Overview

In this workshop you build **StudyMate** from scratch in the AWS Management Console. Students type a question on a web page; the system finds the relevant passages in the university's PDFs and has an AI model write a short answer with the source file.

You only write two small files: a ~40-line Python Lambda function and one HTML page. Everything else is console configuration.

![StudyMate architecture](/images/5-Workshop/architecture.svg?width=100pc)

| | |
| --- | --- |
| **Time** | About 2 hours |
| **Level** | Beginner (you only need an AWS account and an IAM user) |
| **Cost** | Under 1 USD if you clean up afterwards |
| **Region** | US East (N. Virginia) `us-east-1` |

#### Content

1. [Workshop overview](5.1-workshop-overview/)
2. [Prerequisites](5.2-prerequiste/)
3. [Upload documents to Amazon S3](5.3-s3-documents/)
4. [Create the Bedrock Knowledge Base](5.4-knowledge-base/)
5. [Create the Lambda function](5.5-lambda/)
6. [Create the API with API Gateway](5.6-api-gateway/)
7. [Host the web frontend on S3](5.7-frontend/)
8. [Testing and monitoring](5.8-testing-monitoring/)
9. [Clean up resources](5.9-cleanup/)
