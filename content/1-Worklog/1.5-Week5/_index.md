---
title: "Week 5 Worklog"
date: 2026
weight: 1
chapter: false
pre: " <b> 1.5. </b> "
---


### Week 5 Objectives:

* Enable centralized security posture monitoring with Security Hub and AWS Config concepts.
* Understand the migration path from EC2/ASG deployment to ECS/Fargate microservices.
* Compare VPC Peering and Transit Gateway for inter-VPC connectivity.
* Create an event-driven cost optimization workflow with CloudWatch, SNS, and Lambda.

### Tasks to be carried out this week:
| Day | Task                                                                                                                                                                                                   | Start Date | Completion Date | Reference Material                        |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------- | --------------- | ----------------------------------------- |
| 1   | - Study AWS Security Hub and AWS Config.<br> - Enable Security Hub CSPM, review AWS Foundational Security Best Practices, CIS, and PCI DSS standards.                                                                                                  | 04/05/2026 | 04/05/2026      | <https://000018.awsstudygroup.com/> |
| 2   | - Design ECS microservices deployment. <br> - Study Docker packaging, ECR images, Fargate tasks, task definitions, private subnets, NAT Gateway, and service separation. <br>                                              | 05/05/2026 | 05/05/2026      | <https://000016.awsstudygroup.com/><https://000067.awsstudygroup.com/> |
| 3   | - Set up manual VPC Peering. <br> - Create two non-overlapping VPCs, configure routes/security groups, test private connectivity, and learn peering limitations. | 06/05/2026 | 06/05/2026      | <https://000019.awsstudygroup.com/> |
| 4   | - Build Transit Gateway hub-and-spoke networking.<br> - Connect four VPCs through TGW attachments, fix missing propagation, verify inter-VPC ping, and cleanup high-cost TGW resources.<br>                            | 06/05/2026 | 06/05/2026      | <https://000020.awsstudygroup.com/> |\
| 5   | - Optimize EC2 cost with Lambda automation.<br> - Use VPC Flow Logs, CloudWatch Metric Filter, Alarm, SNS, and Lambda boto3 to stop an EC2 instance after detected ICMP traffic.<br>                            | 07/05/2026 | 08/05/2026      | <https://000022.awsstudygroup.com/><https://000074.awsstudygroup.com/> |


### Week 5 Achievements:

* Overview:

During this week, I focused on security posture, containers, and inter-vpc networking. The work was organized from my daily learning notes and adjusted into a weekly internship-report format.

* Learned theory:

- Enable centralized security posture monitoring with Security Hub and AWS Config concepts.
- Understand the migration path from EC2/ASG deployment to ECS/Fargate microservices.
- Compare VPC Peering and Transit Gateway for inter-VPC connectivity.
- Create an event-driven cost optimization workflow with CloudWatch, SNS, and Lambda.
* Hands-on labs:

- Connected security monitoring, container architecture, networking scale, and - automation into one practical week.
- Understood when to use VPC Peering versus Transit Gateway.
- Implemented an event-driven automation workflow from logs to Lambda action.