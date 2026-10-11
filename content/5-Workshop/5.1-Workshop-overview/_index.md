---
title: "Workshop Overview"
date: 2026-09-07
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

## How StudyMate works

Think of StudyMate as **a help desk at the university library**. Each AWS service plays one role:

| Library role | AWS service | What it does |
| --- | --- | --- |
| The desk where students ask | **Amazon S3** (static website) | Serves the chat web page |
| The intake window | **Amazon API Gateway** | Gives the page an HTTPS address to send questions to |
| The desk clerk | **AWS Lambda** | Receives the question, asks the librarian, returns the answer |
| The librarian | **Amazon Bedrock Knowledge Base** | Finds the right pages in the documents |
| The writer | **Amazon Nova Lite** | Reads those pages and writes a short answer |
| The bookshelf | **Amazon S3** (private bucket) | Stores the source PDFs |
| The card catalogue | **Amazon S3 Vectors** | Stores the "meaning" of each passage so it can be found quickly |

## Key concepts

#### RAG (Retrieval-Augmented Generation)
Asking an AI model directly about your university's rules does not work: it has never read them and may invent an answer. RAG adds a step: **retrieve** the relevant passages first, then let the model **generate** an answer using only those passages.

#### Chunking
The Knowledge Base splits each PDF into small pieces (chunks) of a few paragraphs, so it can return just the relevant part instead of a whole 50-page file.

#### Embedding
An embedding model (here **Titan Text Embeddings V2**) turns each chunk into a list of numbers (a vector). Passages with similar meaning get similar vectors, even if they use different words. "Scholarship" and "financial support for top students" end up close together.

#### Vector store
A database built to store vectors and find the nearest ones fast. When a question arrives, it is also turned into a vector, and the vector store returns the closest chunks. This workshop uses **Amazon S3 Vectors** because it costs almost nothing at small scale.

#### IAM role
An IAM user gives permissions to a person; an **IAM role** gives permissions to a service. The Lambda function gets a role that lets it call only the Knowledge Base and the model it needs.

## What you will build

1. A private S3 bucket holding the PDFs.
2. A Bedrock Knowledge Base that indexes the PDFs into S3 Vectors.
3. A Lambda function that calls the Knowledge Base.
4. An HTTP API in API Gateway that exposes the Lambda function.
5. A public S3 bucket hosting the chat page.

{{% notice tip %}}
Work step by step and **test each part before moving on**: Knowledge Base test in the console → Lambda test event → curl against the API → the web page. When something breaks, you will know exactly which step caused it.
{{% /notice %}}
