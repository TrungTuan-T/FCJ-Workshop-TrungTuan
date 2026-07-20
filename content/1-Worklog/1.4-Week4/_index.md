---
title: "Week 4 Worklog"
date: 2026
weight: 1
chapter: false
pre: " <b> 1.4. </b> "
---


### Week 4 Objectives:

* Build bidirectional DNS resolution between simulated on-premises and AWS networks.
* Use AWS CLI to manage S3, SNS, IAM, VPC, and EC2 resources.
* Design backup and restore workflows with AWS Backup.
* Practice dependency-aware cleanup and cost control.

### Tasks to be carried out this week:
| Day | Task                                                                                                                                                                                                   | Start Date | Completion Date | Reference Material                        |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------- | --------------- | ----------------------------------------- |
| 1   | - Implement Route 53 Resolver architecture. <br> - Create simulated on-premises and AWS VPCs, configure VPC peering, route tables, inbound/outbound endpoints, and resolver rules.                                                                                                   | 01/05/2026 | 01/05/2026      | <https://000010.awsstudygroup.com/> |
| 2  | - Practice AWS CLI resource management.<br> -  Install AWS CLI v2, configure credentials, manage S3/SNS/IAM, and create VPC/EC2 infrastructure through commands.<br>                                              | 02/05/2026 | 02/05/2026      | <https://000011.awsstudygroup.com/> |
| 3   | - Create AWS Backup plan and backup vault.<br> - Use tags to assign EC2 resources to backup rules and configure IAM/SNS permissions for backup notifications. | 02/05/2026 | 03/05/2026      | <https://000013.awsstudygroup.com/> |
| 4   | - Test restore and cleanup flow.<br> - Understand recovery points, non-overwrite restore behavior, idempotency tokens, and cleanup order for backup-related resources. <br>                            | 03/05/2026 | 04/05/2026      | <https://000013.awsstudygroup.com/> |


### Week 4 Achievements:

* Overview:

During this week, I focused on dns, cli automation, backup, and recovery. The work was organized from my daily learning notes and adjusted into a weekly internship-report format.

* Learned theory:

- Build bidirectional DNS resolution between simulated on-premises and AWS networks.
- Use AWS CLI to manage S3, SNS, IAM, VPC, and EC2 resources.
- Design backup and restore workflows with AWS Backup.
- Practice dependency-aware cleanup and cost control.
* Hands-on labs:

- Built a manual DNS forwarding model across simulated hybrid networks.
- Used AWS CLI for infrastructure operations instead of relying only on the console.
- Configured backup automation and understood restore behavior for EC2 workloads.


