---
title: "Week 8 Worklog"
date: 2026-10-26
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

### Week 8 Objectives:

* Connect S3 to Lambda so new images are shrunk automatically.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - Create the Thumbnail Lambda and attach the Pillow layer. | 26/10/2026 | 26/10/2026 | <https://docs.aws.amazon.com/lambda/> |
| 3 | - Add an S3 trigger on the uploads/ prefix. | 27/10/2026 | 27/10/2026 | <https://docs.aws.amazon.com/lambda/latest/dg/with-s3.html> |
| 4 | - Write a least-privilege IAM policy for the function. | 28/10/2026 | 28/10/2026 | <https://docs.aws.amazon.com/IAM/> |
| 5 | - Fix the recursive-trigger risk when writing to the same bucket (separate prefixes). | 29/10/2026 | 29/10/2026 | <https://docs.aws.amazon.com/lambda/latest/dg/with-s3.html> |
| 6 | - Translate blog 2.<br>- Write week 8 worklog. | 30/10/2026 | 30/10/2026 | <https://aws.amazon.com/blogs/compute/> |

### Week 8 Achievements:

* An upload to uploads/ produces a thumbnail in thumbnails/ within 1–3 seconds.
* Can read CloudWatch logs to find errors.
