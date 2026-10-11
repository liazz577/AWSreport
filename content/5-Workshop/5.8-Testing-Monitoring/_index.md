---
title: "Testing and Monitoring"
date: 2026-09-07
weight: 8
chapter: false
pre: " <b> 5.8. </b> "
---

## Answer quality test

1. Write 20 questions whose answers you know are in the documents.
2. Ask each one on the web page and record the result in a table like this:

| # | Question | Correct? | Cited source |
| --- | --- | --- | --- |
| 1 | What are the scholarship conditions? | ✅ | quy-che-dao-tao.pdf |
| 2 | ... | | |

3. Ask 2–3 questions outside the documents (e.g. *"What's the weather today?"*) to check that the system does not invent answers.

Result: <span style="background:#fff3c4">[x/20 correct]</span>

## Monitoring with CloudWatch

1. Open **CloudWatch** → **Log groups** → `/aws/lambda/studymate-ask`.
2. Open the latest log stream. Each request logs its **Duration** and any errors.
3. In the Lambda console, the **Monitor** tab shows invocation count, duration and error charts.

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Insert screenshot:</b> Lambda Monitor tab charts → <code>static/images/5-Workshop/5.8/01-monitor.png</code></p>
<!-- ![Lambda Monitor tab charts](/images/5-Workshop/5.8/01-monitor.png?width=90pc) -->

## Common errors

| Error | Cause | Fix |
| --- | --- | --- |
| `AccessDeniedException` | The Lambda role lacks permissions | Check `ACCOUNT_ID`, `KB_ID` and region in the policy (5.5.2) |
| `Task timed out after 3.00 seconds` | Default timeout | Raise the timeout to 30 seconds (5.5.1) |
| CORS error in the browser console | CORS not configured | Configure CORS on the API (5.6.2) |
| "Sorry, I am unable to assist..." for every question | Data not synced, or scanned PDFs | Run Sync (5.4.2); use text-based PDFs |
| `{"message":"Not Found"}` | Wrong URL | Make sure the URL ends with `/ask` |
