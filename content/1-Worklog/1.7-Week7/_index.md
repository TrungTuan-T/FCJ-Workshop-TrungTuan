---
title: "Week 7 Worklog"
date: 2026
weight: 1
chapter: false
pre: " <b> 1.7. </b> "
---


### Week 7 Objectives:

* Automate infrastructure creation with CloudFormation.
* Analyze cost and usage data with S3, Glue, and Athena.
* Compare Savings Plans, Reserved Instances, and Reserved DB Instances.
* Deploy managed Windows file storage with Amazon FSx integrated with Active Directory.
* Protect a web application with AWS WAF in front of an ALB.
### Tasks to be carried out this week:
| Day | Task                                                                                                                                                                                                   | Start Date | Completion Date | Reference Material                        |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------- | --------------- | ----------------------------------------- |
| 1   | - Automate web app and RDS infrastructure with CloudFormation. <br> - Create VPC, public subnets, IGW, route tables, EC2, RDS, security groups, and deployment scripts from one template.                                                                                                   | 13/05/2026 | 13/05/2026      | <https://000037.awsstudygroup.com/> |
| 2   | - Analyze cost and performance with AWS Glue and Athena.<br> - &Create S3 data/query buckets, Glue crawler/catalog, and Athena SQL queries for CUR-style cost analysis.<br>                                              | 14/05/2026 | 14/05/2026      | <https://000040.awsstudygroup.com/> |
| 3   | - Study Savings Plans, RI, and Reserved DB Instances.<br> - Compare commitment scope, payment options, discount trade-offs, utilization risk, and recommendation reading. | 15/05/2026 | 15/05/2026      | <https://000042.awsstudygroup.com/> |
| 4   | - Deploy Amazon FSx for Windows File Server.<br> - Integrate FSx with Microsoft Active Directory, test SMB access from Linux EC2 with cifs-utils/samba-client, and verify cross-node file sync.<br>                            | 16/05/2026 | 16/05/2026      | <https://000025.awsstudygroup.com/> |
| 5   | - Protect web application with AWS WAF. <br> - Place WAF before ALB, connect ALB to EC2 app and RDS database, then review traffic filtering and application protection flow.                                        | 17/05/2026 | 17/05/2026      | <https://000026.awsstudygroup.com/> |


### Week 7 Achievements:

* Overview:

During this week, I focused on infrastructure as code, cost analytics, commitment models, fsx, and waf. The work was organized from my daily learning notes and adjusted into a weekly internship-report format.

* Learned theory:

- Automate infrastructure creation with CloudFormation.
- Analyze cost and usage data with S3, Glue, and Athena.
- Compare Savings Plans, Reserved Instances, and Reserved DB Instances.
- Deploy managed Windows file storage with Amazon FSx integrated with Active Directory.
- Protect a web application with AWS WAF in front of an ALB.
* Hands-on labs:

- Moved from manual infrastructure operations toward repeatable CloudFormation templates.
- Understood how cost data can be stored, cataloged, and queried serverlessly.
- Practiced managed file storage and web application protection patterns.