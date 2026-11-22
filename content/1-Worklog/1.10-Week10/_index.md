---
title: "Week 10 Worklog"
date: 2026-11-09
weight: 10
chapter: false
pre: " <b> 1.10. </b> "
---

### Week 10 Objectives:

* Rebuild the whole system from code and add tests.

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - Write a CloudFormation template for all infrastructure. | 05/10/2026 | 05/10/2026 | <https://docs.aws.amazon.com/cloudformation/> |
| 3 | - Resolve the circular dependency between S3 notification and Lambda permission. | 06/10/2026 | 06/10/2026 | <https://docs.aws.amazon.com/cloudformation/> |
| 4 | - Write deploy.sh and destroy.sh. | 07/10/2026 | 07/10/2026 | - |
| 5 | - Write end-to-end tests with moto (mocked AWS). | 08/10/2026 | 08/10/2026 | <https://docs.getmoto.org/> |
| 6 | - Write week 10 worklog. | 09/10/2026 | 09/10/2026 | - |

### Week 10 Achievements:

* One deploy.sh command builds the system in minutes.
* All 7 automated tests pass.
