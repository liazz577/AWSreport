---
title: "Blog 2"
date: 2026-09-07
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

# Simplified model access in Amazon Bedrock

| Item | Detail |
| --- | --- |
| **Original post** | [Simplified model access in Amazon Bedrock](https://aws.amazon.com/blogs/security/simplified-amazon-bedrock-model-access/) |
| **Source** | AWS Security Blog |
| **Topic** | Security · Access management |
| **Translated on** | <span style="background:#fff3c4">[dd/mm/2026]</span> |

## Summary

Amazon Bedrock retired the "Model access" page: serverless models are enabled by default in all commercial regions, and access is controlled with IAM policies and Service Control Policies like other AWS services. Anthropic models still require a one-time use-case form.

## Relevance to StudyMate

Because models are on by default, scoping permissions matters more. In StudyMate, the Lambda's IAM policy allows only the Nova Lite model and the project's own Knowledge Base (section 5.5.2).

## Translation

<span style="background:#fff3c4">[The Vietnamese translation lives on the Vietnamese version of this page]</span>
