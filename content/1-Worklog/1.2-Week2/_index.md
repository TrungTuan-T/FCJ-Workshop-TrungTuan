---
title: "Week 2 Worklog"
date: 2026
weight: 1
chapter: false
pre: " <b> 1.2. </b> "
---


### Week 2 Objectives:

* Move from local database usage to managed database architecture on AWS.
* Practice network isolation between public web servers and private databases.
* Use resource tags and resource groups to organize cloud assets.
* Prepare the base knowledge for later automation and CI/CD work.

### Tasks to be carried out this week:
| Day | Task                                                                                                                                                                                                   | Start Date | Completion Date | Reference Material                        |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------- | --------------- | ----------------------------------------- |
| 1   | - Study Amazon RDS with a Node.js application. <br> - Create a MySQL RDS instance in private subnets, configure DB subnet group, and connect it from an EC2 web server.                                                                                                   | 24/04/2026 | 26/04/2026      | <https://000005.awsstudygroup.com/> |
| 2   | - Apply security group chaining for database protection.<br> - Allow MySQL traffic only from the EC2 Web App Security Group and avoid public database exposure. <br>                                              | 25/04/2026 | 26/04/2026      | <https://000005.awsstudygroup.com/><https://000003.awsstudygroup.com/> |
| 3   | - Practice resource tagging strategy.<br> - Create EC2 instances for different environments and manage tags in bulk through the EC2 Tags console.<br>  | 27/04/2026   | 28/04/2026      | <https://000027.awsstudygroup.com/> |
| 4   | - Create tag-based Resource Groups. <br> - Preview and save resource groups based on EC2 tag criteria to improve resource discovery and management.<br>                            | 28/04/2026   | 29/04/2026      | <https://000027.awsstudygroup.com/> |


### Week 2 Achievements:

* Overview:

During this week, I focused on rds, resource tagging, and application deployment foundation. The work was organized from my daily learning notes and adjusted into a weekly internship-report format.

* Learned theory:

- Move from local database usage to managed database architecture on AWS.
- Practice network isolation between public web servers and private databases.
- Use resource tags and resource groups to organize cloud assets.
- Prepare the base knowledge for later automation and CI/CD work.

* Hands-on labs:

Deployed a basic web application connected to Amazon RDS.
Applied a cleaner public/private network model for application and database layers.
Used tags and resource groups to organize resources for later governance tasks.

