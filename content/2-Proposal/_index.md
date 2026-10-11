---
title: "Proposal"
date: 2026-09-07
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# StudyMate
## A Student Handbook Q&A Chatbot on AWS

# 1. Executive Summary

StudyMate is a chat web page that helps students look up academic regulations, the student handbook and administrative rules in Vietnamese. It uses **RAG (Retrieval-Augmented Generation)**: it first retrieves relevant document passages, then has a language model write a short answer that cites the source file. The whole system is **serverless** on AWS, so there are no servers to manage, and the demo costs an estimated less than 1 USD per month.

# 2. Problem Statement

## Current Problem

- Regulations and the student handbook run to dozens of pages spread across several PDFs.
- Students struggle to find specific information (scholarship conditions, conduct scoring, leave of absence...).
- Repeated questions pile up at the academic office and with academic advisors.

## Solution

- Load all documents into an **Amazon Bedrock Knowledge Base**.
- Students ask questions in natural language on a web page.
- The system answers **only from the university's documents** and always shows sources so students can verify.

## Benefits

- Fast, always-available lookups for students.
- Fewer repeated questions for the academic office.
- Easy updates: upload a new file to S3 and click Sync.
- Very low cost thanks to the serverless design and S3 Vectors.

# 3. Solution Architecture

## Solution Architecture

![StudyMate architecture](/images/5-Workshop/architecture-en.svg?width=100pc)

**Question flow (1–6):** (1) The student loads the static web page from Amazon S3 → (2) the page sends the question to Amazon API Gateway (`POST /ask`) → (3) API Gateway invokes AWS Lambda → (4) Lambda calls the `retrieve_and_generate` API of the Amazon Bedrock Knowledge Base → (5a) the Knowledge Base uses Titan Text Embeddings V2 to turn the question into a vector, (5b) then finds the 5 nearest chunks in Amazon S3 Vectors → (6) Amazon Nova Lite writes the answer with sources. The result returns through Lambda and API Gateway to the browser.

**Ingestion flow (A–D):** (A) The administrator uploads PDFs to a private S3 bucket → (B) on Sync, the Knowledge Base reads and chunks them → (C) Titan Text Embeddings V2 turns each chunk into a vector → (D) the vectors are stored in Amazon S3 Vectors.

**Monitoring and security:** Lambda writes logs to Amazon CloudWatch. Two IAM roles (one for Lambda, one for the Knowledge Base) grant only the permissions each needs.

## AWS Services Used

| Service | Role |
| --- | --- |
| Amazon S3 | Stores source PDFs; hosts the static web UI |
| Amazon Bedrock Knowledge Bases | Chunks documents, creates embeddings, retrieves and generates answers |
| Amazon S3 Vectors | Low-cost vector store for the Knowledge Base |
| Amazon Titan Text Embeddings V2 | Converts text into vectors |
| Amazon Nova Lite | Language model that writes answers |
| AWS Lambda | Receives the question, calls Bedrock, returns JSON |
| Amazon API Gateway | Provides an HTTPS endpoint for the web page |
| Amazon CloudWatch | Logs, error and duration monitoring |
| AWS IAM | Least-privilege permissions for each service |
| AWS Budgets | Cost alerts |

## Component Design

### Frontend
- A single plain `index.html` (HTML, CSS, JavaScript), no framework.
- Hosted with S3 static website hosting.

### Backend
- A Python 3.12 Lambda function of about 40 lines.
- Calls Bedrock's `retrieve_and_generate` API: one call both retrieves passages and generates the answer.
- Validates input (question of 1–500 characters).

### Data
- Source documents: 2–3 text-based PDFs.
- Vectors stored in S3 Vectors, created and managed by the Knowledge Base.

### Security
- The documents bucket stays private; only the web bucket is publicly readable.
- Lambda may only call the specific Knowledge Base and model it needs.
- API Gateway throttling limits request rates.

# 4. Technical Implementation

## Implementation Phases

1. Prepare the account, region and cost alerts.
2. Upload documents to S3; create and test the Knowledge Base.
3. Write the Lambda function; configure IAM.
4. Create the API Gateway; configure CORS.
5. Write and host the web UI.
6. Test, monitor, clean up.

## Technical Requirements

### Programming Languages
- Python 3.12 (Lambda)
- HTML, CSS, JavaScript (UI)

### Libraries
- boto3 (included in the Lambda runtime)

### Tools
- AWS Management Console, AWS CLI, Visual Studio Code, Git, Hugo

# 5. Timeline & Milestones

| Week | Work |
| --- | --- |
| 1–5 | AWS fundamentals: IAM, VPC, EC2, S3, RDS, DynamoDB, Lambda, API Gateway |
| 6 | Explore Bedrock and RAG; write the proposal |
| 7 | Build the Knowledge Base |
| 8 | Write the Lambda function and IAM policy |
| 9 | API Gateway and web UI |
| 10 | Security, monitoring, quality evaluation |
| 11 | Write the workshop; publish the report on GitHub Pages |
| 12 | Demo, cleanup, wrap-up |

# 6. Budget Estimation

## Infrastructure Cost Estimate

Assumptions: us-east-1; 3 PDFs (about 50,000 tokens); 1,000 questions/month, each about 3,000 input and 300 output tokens.

| Item | Estimated cost (USD/month) |
| --- | --- |
| Amazon S3 (documents + website, under 1 GB) | ~0.03 |
| Amazon S3 Vectors (storage + queries) | < 0.01 |
| Titan Text Embeddings V2 | < 0.01 |
| Amazon Nova Lite (3M input, 0.3M output tokens) | ~0.25 |
| AWS Lambda, API Gateway, CloudWatch (within Free Tier) | ~0.00 |
| **Total** | **< 1.00** |

{{% notice warning %}}
These are estimates based on list prices, which can change. <span style="background:#fff3c4">[Insert your AWS Pricing Calculator link here]</span>
{{% /notice %}}

### Cost Control Guidelines

- Create a 5 USD AWS Budget from day one.
- Do **not** pick Amazon OpenSearch Serverless as the vector store: it bills a minimum hourly capacity even when idle, which can reach hundreds of USD per month.
- Delete all resources after the demo (section 5.9).

# 7. Risk Assessment

## Risk Matrix

| Risk | Likelihood | Impact | Mitigation |
| --- | --- | --- | --- |
| Model answers wrongly or invents facts | Medium | High | Answer only from documents; always show sources |
| Unexpected charges | Low | Medium | AWS Budgets; clean up after the demo |
| API abuse | Low | Medium | API Gateway throttling; question length limit |
| Scanned PDFs without text | Medium | Medium | Use text-based PDFs or enable advanced parsing |

# 8. Expected Outcomes

## Technical Outcomes

- A working chat page that answers within a few seconds and cites sources.
- At least 16 of 20 test questions answered correctly.
- A workshop that rebuilds the whole system from scratch.

## Practical Value

- Can be extended into an academic assistant for the whole university.
- A foundation for the AI assistant in student-management projects.
