---
title: "Week 10 Worklog"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 1.10. </b> "
---


# Week 10 Worklog

## Week 10 Objectives

In week 10, the main goal was to explore the **cost optimization and system security** content group on AWS. This is part of the **Optimize / Optimizing system** group in the AWS Cloud Journey, focusing on security, reliability, performance, cost optimization, and system operations after deployment to AWS. ([Cloud Journey][1])

Key topics for the week include:

* Getting an overview of cost optimization on AWS.
* Exploring **AWS Budgets** to create budgets and cost alerts.
* Exploring **AWS Cost Explorer** to analyze service usage costs.
* Exploring **AWS KMS** to manage keys and encrypt data.
* Exploring **AWS WAF** to protect web applications and APIs.
* Exploring **AWS Security Hub** to check and assess security standards.
* Noting best practices for security and cost management on AWS.

---

## Tasks to be carried out this week

| No. | Task                                                                                                                                                                                              | Start Date | Completion Date | Reference Material                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| --- | --- | --- | --- | --- |
| **1** | Get an overview of the Optimize group on AWS, including cost optimization, security, reliability, performance, and system operations.                                                            | 26/05/2026 | 26/05/2026 | [https://cloudjourney.awsstudygroup.com/3-optimize/](https://cloudjourney.awsstudygroup.com/3-optimize/)                                                                                                                                                                                                                                                                                                                                                                                                                   |
| **2** | Explore AWS Budgets and Cost Explorer, how to create budgets, track costs, analyze cost by service, and set alerts when thresholds are exceeded.                                                | 27/05/2026 | 27/05/2026 | [https://000001.awsstudygroup.com/7-monitoring-v%C3%A0-t%E1%BB%91i-%C6%B0u-chi-ph%C3%AD/](https://000001.awsstudygroup.com/7-monitoring-v%C3%A0-t%E1%BB%91i-%C6%B0u-chi-ph%C3%AD/) <br> [https://docs.aws.amazon.com/cost-management/latest/userguide/budgets-managing-costs.html](https://docs.aws.amazon.com/cost-management/latest/userguide/budgets-managing-costs.html) <br> [https://docs.aws.amazon.com/cost-management/latest/userguide/ce-what-is.html](https://docs.aws.amazon.com/cost-management/latest/userguide/ce-what-is.html) |
| **3** | Explore AWS KMS and the role of the Key Management Service in creating, managing encryption keys, and protecting data across AWS services.                                                    | 28/05/2026 | 28/05/2026 | [https://000033.awsstudygroup.com/](https://000033.awsstudygroup.com/) <br> [https://docs.aws.amazon.com/kms/](https://docs.aws.amazon.com/kms/)                                                                                                                                                                                                                                                                                                                                                                           |
| **4** | Explore AWS WAF and how the Web Application Firewall protects websites, APIs, and applications from common attacks such as SQL Injection, XSS, or bot traffic.                                 | 29/05/2026 | 29/05/2026 | [https://000026.awsstudygroup.com/](https://000026.awsstudygroup.com/) <br> [https://aws.amazon.com/waf/](https://aws.amazon.com/waf/) <br> [https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-control-access-aws-waf.html](https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-control-access-aws-waf.html)                                                                                                                                                           |
| **5** | Explore AWS Security Hub, consolidate security and cost-optimization knowledge; note principles such as monitoring costs regularly, limiting access, encrypting data, and protecting web apps. | 30/05/2026 | 30/05/2026 | [https://000018.awsstudygroup.com/](https://000018.awsstudygroup.com/) <br> [https://docs.aws.amazon.com/securityhub/latest/userguide/what-is-securityhub.html](https://docs.aws.amazon.com/securityhub/latest/userguide/what-is-securityhub.html) <br> [https://cloudjourney.awsstudygroup.com/3-optimize/](https://cloudjourney.awsstudygroup.com/3-optimize/)                                                                                                                                                                         |

---

## Week 10 Achievements

### Overview

This week, I explored services that support **cost optimization and strengthen system security on AWS**. The content has two main groups: cost management with **AWS Budgets** and **AWS Cost Explorer**, and security with **AWS KMS**, **AWS WAF**, and **AWS Security Hub**. AWS Budgets helps track costs and usage against budgets, while Cost Explorer helps view and analyze AWS usage costs. ([AWS Documentation][2])

### Knowledge gained

After completing week 10, I have understood:

* The role of cost optimization when deploying systems on AWS.
* How to use **AWS Budgets** to create budgets, set alert thresholds, and track usage costs.
* How to use **AWS Cost Explorer** to analyze cost by service, time, account, or tag.
* That **AWS KMS** is a key management and encryption service used to protect data in AWS services or in your own applications. ([AWS Documentation][3])
* That **AWS WAF** is a Web Application Firewall used to protect websites, APIs, and applications from bots, exploits, and application-layer attacks such as SQL Injection or Cross-site Scripting. ([Amazon Web Services, Inc.][4])
* That **AWS Security Hub** helps check security controls and create findings to assess compliance with security best practices. ([AWS Documentation][5])
* The importance of combining cost monitoring, sensible IAM permissions, data encryption, and WAF-based app protection.

---

## Practice

During the learning and exploration process, I was able to:

* Access AWS Billing and Cost Management on the AWS Console.
* Learn how to view AWS service usage costs.
* Learn how to create a budget to track monthly costs.
* Learn how to configure alerts when costs exceed a threshold.
* Learn how to view cost charts with Cost Explorer.
* Explore AWS KMS, Customer Managed Key, and AWS Managed Key.
* Learn how AWS KMS supports data encryption for services like S3, EBS, or RDS.
* Explore AWS WAF, Web ACL, Rule, and Managed Rule Group.
* Learn how AWS WAF can attach to CloudFront, Application Load Balancer, or API Gateway.
* Explore AWS Security Hub and the role of security findings in security assessment.
* Note common errors such as Cost Explorer not enabled, no budget created, missing IAM permissions, wrong WAF rule configuration, or encryption not enabled for important data.

---

## Week 10 Service Summary

| Service              | Main Purpose                       | Role in the system                                             |
| -------------------- | ---------------------------------- | -------------------------------------------------------------- |
| **AWS Budgets**      | Create budgets and cost alerts     | Control costs, avoid unexpected overruns                       |
| **AWS Cost Explorer**| Analyze AWS usage costs            | See which services consume the most cost                      |
| **AWS KMS**          | Manage keys and encrypt data       | Protect data at rest and in use                                |
| **AWS WAF**          | Protect websites and APIs          | Block malicious requests, bot traffic, SQL Injection, XSS      |
| **AWS Security Hub** | Assess security posture            | Aggregate security findings and check against security standards |

---

## Week 10 Summary

**Week 10:** Explore AWS Budgets, Cost Explorer, KMS, WAF, and cost optimization and system security. Understand how to track costs, create budget alerts, analyze service usage, encrypt data, protect web apps/APIs, and assess the security posture on AWS.

[1]: https://cloudjourney.awsstudygroup.com/3-optimize/ "Optimizing the system :: The First Cloud Journey"
[2]: https://docs.aws.amazon.com/cost-management/latest/userguide/budgets-managing-costs.html?utm_source=chatgpt.com "Managing your costs with AWS Budgets"
[3]: https://docs.aws.amazon.com/kms/?utm_source=chatgpt.com "AWS Key Management Service Documentation"
[4]: https://aws.amazon.com/waf/?utm_source=chatgpt.com "Web Application Firewall - Web API Protection - AWS WAF"
[5]: https://docs.aws.amazon.com/securityhub/latest/userguide/what-is-securityhub.html?utm_source=chatgpt.com "Introduction to AWS Security Hub CSPM"
