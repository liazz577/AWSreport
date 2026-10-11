---
title: "Upload Documents to S3"
date: 2026-09-07
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

## What is Amazon S3?

Amazon S3 is AWS's **online storage**, similar to Google Drive. A **bucket** is a top-level folder; each file inside is an **object**. This bucket stays **private** because only the Knowledge Base needs to read it.

## Create the bucket

1. Open the **Amazon S3** console → **Create bucket**.
2. **Bucket type:** General purpose.
3. **Bucket name:** `studymate-docs-<your-name>` (bucket names must be unique across all AWS accounts, so add your name).
4. Keep **Block all public access** checked.
5. Leave the other settings as default → **Create bucket**.

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Insert screenshot:</b> Create bucket form → <code>static/images/5-Workshop/5.3/01-create-bucket.png</code></p>
<!-- ![Create bucket form](/images/5-Workshop/5.3/01-create-bucket.png?width=90pc) -->

## Upload the PDFs

1. Open the new bucket → **Upload**.
2. Drag in your PDFs → **Upload**.

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Insert screenshot:</b> object list showing the uploaded PDFs → <code>static/images/5-Workshop/5.3/02-objects.png</code></p>
<!-- ![object list showing the uploaded PDFs](/images/5-Workshop/5.3/02-objects.png?width=90pc) -->

{{% notice tip %}}
Use clear file names such as `quy-che-dao-tao.pdf`. The chatbot shows the file name as its source, so readable names make answers easier to trust.
{{% /notice %}}
