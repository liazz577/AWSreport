---
title: "Week 8 Worklog"
date: 2026-10-26
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

### Week 8 Objectives:

* Write a Lambda function that queries the Knowledge Base, with least-privilege IAM.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - Read the boto3 retrieve_and_generate API docs. | 26/10/2026 | 26/10/2026 | <https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/bedrock-agent-runtime.html> |
| 3 | - Write the studymate-ask Lambda function. | 27/10/2026 | 27/10/2026 | - |
| 4 | - Write an IAM inline policy allowing only the specific Knowledge Base and model. | 28/10/2026 | 28/10/2026 | <https://docs.aws.amazon.com/IAM/> |
| 5 | - Fix AccessDeniedException and timeout errors using CloudWatch logs. | 29/10/2026 | 29/10/2026 | - |
| 6 | - Translate blog 2.<br>- Write week 8 worklog. | 30/10/2026 | 30/10/2026 | <https://aws.amazon.com/blogs/> |

### Week 8 Achievements:

* The Lambda returns JSON with the answer and a source list.
* Can read logs to find the cause of errors.
