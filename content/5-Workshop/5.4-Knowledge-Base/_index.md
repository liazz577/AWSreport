---
title: "Create the Knowledge Base"
date: 2026-09-07
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

## What the Knowledge Base does

This is the core of StudyMate, but AWS does most of the work. When you click **Sync**, the Knowledge Base:

1. Reads the PDFs from your S3 bucket.
2. Splits them into chunks.
3. Calls **Titan Text Embeddings V2** to turn each chunk into a vector.
4. Stores the vectors in **Amazon S3 Vectors**.

When a question arrives, it is turned into a vector too, and the Knowledge Base returns the closest chunks.

#### Content

1. [Create the Knowledge Base](5.4.1-create-knowledge-base/)
2. [Sync and test](5.4.2-sync-and-test/)
