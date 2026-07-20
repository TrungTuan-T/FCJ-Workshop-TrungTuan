---
title: "Week 8 Worklog"
date: 2026
weight: 1
chapter: false
pre: " <b> 1.8. </b> "
---


### Week 8 Objectives:

* Limit delegated administrators with permission boundaries.
* Define full-stack infrastructure using AWS CDK instead of manual console work or long YAML templates.
* Migrate databases with AWS DMS while minimizing downtime.
* Design IAM role conditions based on practical network constraints.

### Tasks to be carried out this week:
| Day | Task                                                                                                                                                                                                   | Start Date | Completion Date | Reference Material                        |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------- | --------------- | ----------------------------------------- |
| 1   | - Limit user rights with IAM Permission Boundary.<br> - Design tiered boundaries for DoiPho and Dev users to prevent privilege escalation and block RDS access.                                                                                              | 18/05/2026 | 18/05/2026      | <https://000030.awsstudygroup.com/> |
| 2   | - Build basic infrastructure with AWS CDK.<br> - Use TypeScript CDK to create VPC, public EC2, private RDS MariaDB, security groups, and UserData database seeding.<br>                                              | 19/05/2026 | 19/05/2026      | <https://000038.awsstudygroup.com/> |
| 3   | - Perform database schema conversion and migration.<br> - Migrate an EC2 web app database from RDS MySQL to RDS MariaDB using DMS endpoints and full load plus CDC replication. | 20/05/2026 | 20/05/2026      | <https://000043.awsstudygroup.com/> |
| 4   | - Test IAM Role and Condition design.<br> - Adapt the lab from IP-based conditions to aws:RequestedRegion because of dynamic school network IPs, then verify Singapore allow and N. Virginia deny behavior.<br>                            | 21/05/2026 | 21/05/2026      | <https://000044.awsstudygroup.com/> |


### Week 8 Achievements:

* Overview:

During this week, I focused on advanced iam, cdk, database migration, and conditional access. The work was organized from my daily learning notes and adjusted into a weekly internship-report format.

* Learned theory:

- Limit delegated administrators with permission boundaries.
- Define full-stack infrastructure using AWS CDK instead of manual console work or long YAML templates.
- Migrate databases with AWS DMS while minimizing downtime.
- Design IAM role conditions based on practical network constraints.
* Hands-on labs:

- Understood permission boundaries as a maximum-permission guardrail, not a normal allow policy.
- Created infrastructure with CDK and connected application/database layers securely.
- Practiced heterogeneous database migration and adapted IAM conditions to real environment constraints.

