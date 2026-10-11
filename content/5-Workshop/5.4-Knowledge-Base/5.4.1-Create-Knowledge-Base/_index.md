---
title: "Create the Knowledge Base"
date: 2026-09-07
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

## Step 1: Start the wizard

1. Open the **Amazon Bedrock** console.
2. In the left menu, choose **Knowledge Bases** → **Create** → **Knowledge Base with vector store**.

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Insert screenshot:</b> Create menu with Knowledge Base with vector store → <code>static/images/5-Workshop/5.4/01-create-menu.png</code></p>
<!-- ![Create menu with Knowledge Base with vector store](/images/5-Workshop/5.4/01-create-menu.png?width=90pc) -->

## Step 2: Knowledge Base details

1. **Knowledge Base name:** `studymate-kb`.
2. **IAM permissions:** Create and use a new service role. Bedrock creates a role that can read your bucket and call the embedding model.
3. **Data source:** Amazon S3.
4. Click **Next**.

## Step 3: Configure the data source

1. **S3 URI:** click **Browse S3** and choose `studymate-docs-<your-name>`.
2. **Parsing strategy** and **Chunking strategy:** keep **Default**.
3. Click **Next**.

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Insert screenshot:</b> data source with the S3 URI selected → <code>static/images/5-Workshop/5.4/02-data-source.png</code></p>
<!-- ![data source with the S3 URI selected](/images/5-Workshop/5.4/02-data-source.png?width=90pc) -->

## Step 4: Embeddings model and vector store

1. **Embeddings model:** **Titan Text Embeddings V2**.
2. **Vector store:** **Quick create a new vector store** → **Amazon S3 Vectors**.
3. Click **Next**, review, then **Create Knowledge Base**. Creation takes a few minutes.

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Insert screenshot:</b> embeddings model and S3 Vectors selection → <code>static/images/5-Workshop/5.4/03-vector-store.png</code></p>
<!-- ![embeddings model and S3 Vectors selection](/images/5-Workshop/5.4/03-vector-store.png?width=90pc) -->

{{% notice warning %}}
Do **not** choose Amazon OpenSearch Serverless. It charges a minimum hourly capacity even when idle, which can reach hundreds of USD per month.
{{% /notice %}}

## Step 5: Note the Knowledge Base ID

On the Knowledge Base page, copy the **Knowledge Base ID** (10 characters, e.g. `ABCDEFGHIJ`). You will need it in section 5.5.

{{% notice note %}}
Console labels can change slightly over time. If a button name differs, look for the option with the same meaning.
{{% /notice %}}
