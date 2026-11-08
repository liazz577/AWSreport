---
title: "Automate with CloudFormation"
date: 2026-09-07
weight: 9
chapter: false
pre: " <b> 5.9. </b> "
---

Everything in 5.3–5.7 can be created by one template. This is **Infrastructure as Code**: the system is described in a file, so it can be rebuilt identically, reviewed and versioned.

## What the template contains
`infra/template.yaml` in the source package creates: the image bucket (CORS, S3 trigger), the DynamoDB table, both Lambda functions with their IAM roles and log groups, the HTTP API with three routes and throttling, and the website bucket.

## A circular dependency to solve
The bucket's trigger needs the function, the function's permission needs the bucket's ARN, and the function's IAM policy also needs the bucket. Referencing the bucket resource from both would create a cycle CloudFormation refuses. The template breaks it by **building the bucket name from the stack name**: `${AWS::StackName}-images-${AWS::AccountId}-${AWS::Region}`. The permission and the policy use that string, not a reference to the bucket.

## Deploy with one command
In CloudShell, upload `snapthumb.zip`, then:

```bash
unzip -oq snapthumb.zip && cd snapthumb && bash deploy.sh
```

`deploy.sh` runs four steps: create or update the stack; package the Thumbnail function with Pillow; upload both functions' code; upload the web page with `config.js` filled in. It prints the website address at the end.

<p style="background:#fff3c4;padding:8px 12px;border-radius:4px">📷 <b>Insert screenshot:</b> CloudFormation stack Resources tab → <code>static/images/5-Workshop/5.9/01-stack.png</code></p>
<!-- ![CloudFormation stack Resources tab](/images/5-Workshop/5.9/01-stack.png?width=90pc) -->

## Automated tests
`backend/test_app.py` runs the whole flow against mocked AWS (moto): create an upload link, upload, run the thumbnail handler, list, delete, plus broken files, transparent PNGs, portrait and small images, and invalid input.
