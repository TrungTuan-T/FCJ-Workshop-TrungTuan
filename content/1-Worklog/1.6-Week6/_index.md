---
title: "Week 6 Worklog"
date: 2026
weight: 1
chapter: false
pre: " <b> 1.6. </b> "
---


### Week 6 Objectives:

* Automate application deployment to EC2 with CodePipeline and CodeDeploy.
* Restrict EC2 operations with tag-based IAM conditions.
* Install Grafana and connect it to CloudWatch metrics through IAM Role access.
* Manage EC2 fleets using Systems Manager and collect memory metrics for right-sizing.
* Analyze S3 access and encryption behavior with KMS, CloudTrail, and Athena.

### Tasks to be carried out this week:
| Day | Task                                                                                                                                                                                                   | Start Date | Completion Date | Reference Material                        |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------- | --------------- | ----------------------------------------- |
| 1   | - Deploy applications to EC2 with AWS CodePipeline.<br> - Connect GitHub, CodePipeline, S3 artifacts, CodeDeploy, AppSpec hooks, PM2 scripts, and EC2 deployment agent.                                                                                                | 08/05/2026 | 08/05/2026      | <https://000017.awsstudygroup.com/><https://000023.awsstudygroup.com/>   |
| 2   | - Manage EC2 access with resource tags through IAM. <br> - Create policies that enforce Environment=Test during creation and restrict start/stop/terminate actions to tagged instances.<br>                                              | 09/05/2026 | 09/05/2026      | <https://000028.awsstudygroup.com/> |
| 3   | - Install Grafana on EC2 and connect CloudWatch.<br> - Create VPC/SG/EC2, open port 3000, attach IAM Role, and visualize CPUUtilization through Grafana. | 10/05/2026 | 10/05/2026      | <https://000029.awsstudygroup.com/> |
| 4   | - Manage patches and commands with AWS Systems Manager. <br>Attach AmazonSSMManagedInstanceCore, troubleshoot Managed Nodes offline status, and run commands across instances.  <br>                            | 10/05/2026 | 10/05/2026      | <https://000031.awsstudygroup.com/> |
| 5   | - Choose correct EC2 size using CloudWatch Agent. <br> - Install CloudWatch Agent, collect RAM metrics, and prepare data for Compute Optimizer and Cost Explorer right-sizing analysis.                                                                                    | 11/05/2026 | 11/05/2026      | <https://000032.awsstudygroup.com/> |
| 6   | - Encrypt at rest with AWS KMS and audit access. <br> - Use CloudTrail data events and Athena SQL to detect access attempts, then validate KMS deny behavior when Decrypt permission is missing.                                                        | 12/05/2026 | 12/05/2026      | <https://000033.awsstudygroup.com/> |

### Week 6 Achievements:

* Overview:

During this week, I focused on ci/cd, iam governance, monitoring, systems management, and encryption. The work was organized from my daily learning notes and adjusted into a weekly internship-report format.

* Learned theory:

- Automate application deployment to EC2 with CodePipeline and CodeDeploy.
- Restrict EC2 operations with tag-based IAM conditions.
- Install Grafana and connect it to CloudWatch metrics through IAM Role access.
- Manage EC2 fleets using Systems Manager and collect memory metrics for right-sizing.
- Analyze S3 access and encryption behavior with KMS, CloudTrail, and Athena.
* Hands-on labs:

- Built a working CI/CD deployment flow and resolved common CodeDeploy/IAM/AppSpec issues.
- Practiced practical governance using tags, IAM conditions, and encryption controls.
- Improved operational visibility using Grafana, CloudWatch Agent, and Systems Manager.
