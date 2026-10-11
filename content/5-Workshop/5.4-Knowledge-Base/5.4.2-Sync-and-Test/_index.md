---
title: "Sync and Test"
date: 2026-09-07
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

## Sync the data source

Creating the Knowledge Base does not read the documents yet. You must run a **Sync**.

1. On the `studymate-kb` page, scroll to **Data source**.
2. Select the data source → **Sync**.
3. Wait until the sync status shows it has completed.

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Insert screenshot:</b> data source with sync completed → <code>static/images/5-Workshop/5.4/04-sync.png</code></p>
<!-- ![data source with sync completed](/images/5-Workshop/5.4/04-sync.png?width=90pc) -->

{{% notice tip %}}
Whenever you add or change a PDF in the bucket, run **Sync** again. That is all it takes to update StudyMate's knowledge.
{{% /notice %}}

## Test in the console

1. Click **Test Knowledge Base** on the right side of the page.
2. Click **Select model** → **Amazon** → **Nova Lite** → **Apply**.
3. Ask a question whose answer is in your documents, e.g. *"What are the conditions for a scholarship?"*
4. The answer should include citation numbers; click one to see the source passage.

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Insert screenshot:</b> test panel with an answer and its citation → <code>static/images/5-Workshop/5.4/05-test.png</code></p>
<!-- ![test panel with an answer and its citation](/images/5-Workshop/5.4/05-test.png?width=90pc) -->

{{% notice note %}}
**If this test works, the hardest part is done.** The remaining sections only expose this capability through a web page.
{{% /notice %}}
