---
title: "Week 9 Worklog"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.9. </b> "
---


# Week 9 Worklog

## Week 9 Objectives

In week 9, the main goal was to explore AWS services for **system monitoring, logging, and activity tracking**, including **Amazon CloudWatch** and **AWS CloudTrail**. These are important services that help administrators monitor resource performance, detect errors, create alerts, and review the activity history in an AWS account.

This week's content belongs to the **Optimize / Optimizing system on AWS** group, focusing on operations, monitoring, security, performance, and system optimization after deployment to AWS. ([Cloud Journey][1])

Key topics for the week include:

* Getting an overview of system monitoring on AWS.
* Exploring **Amazon CloudWatch Metrics**.
* Exploring **Amazon CloudWatch Logs** and **CloudWatch Logs Insights**.
* Learning how to create a **CloudWatch Alarm**.
* Learning how to create a **CloudWatch Dashboard**.
* Exploring **AWS CloudTrail** to record API call history and user activity.
* Distinguishing the roles of CloudWatch and CloudTrail in system operations.

---

## Tasks to be carried out this week

| No. | Task                                                                                                                                                          | Start Date | Completion Date | Reference Material                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| --- | --- | --- | --- | --- |
| **1** | Get an overview of system monitoring on AWS and the role of CloudWatch and CloudTrail in operations, troubleshooting, and security.                           | 22/05/2026 | 22/05/2026 | [https://cloudjourney.awsstudygroup.com/3-optimize/](https://cloudjourney.awsstudygroup.com/3-optimize/) <br> [https://000008.awsstudygroup.com/](https://000008.awsstudygroup.com/) <br> [https://docs.aws.amazon.com/decision-guides/latest/cloudtrail-or-cloudwatch/cloudtrail-or-cloudwatch.html](https://docs.aws.amazon.com/decision-guides/latest/cloudtrail-or-cloudwatch/cloudtrail-or-cloudwatch.html)                                                                                                                                                                                                                                                                                                                                                                                                            |
| **2** | Explore CloudWatch Metrics, how to view metrics for EC2, RDS, EBS, and other AWS services; explore namespace, dimension, and statistic.                      | 22/05/2026 | 22/05/2026 | [https://000008.awsstudygroup.com/3-cloud-watch-metric/](https://000008.awsstudygroup.com/3-cloud-watch-metric/) <br> [https://000008.awsstudygroup.com/3-cloud-watch-metric/3.1-viewing-metrics/](https://000008.awsstudygroup.com/3-cloud-watch-metric/3.1-viewing-metrics/) <br> [https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/working_with_metrics.html](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/working_with_metrics.html)                                                                                                                                                                                                                                                                                                                                                                                                            |
| **3** | Explore CloudWatch Logs, Log Group, Log Stream, Logs Insights, and how to use logs to support error investigation.                                           | 23/05/2026 | 23/05/2026 | [https://000008.awsstudygroup.com/4-cloud-watch-log/](https://000008.awsstudygroup.com/4-cloud-watch-log/) <br> [https://000008.awsstudygroup.com/4-cloud-watch-log/4.1-cloud-watch-logs/](https://000008.awsstudygroup.com/4-cloud-watch-log/4.1-cloud-watch-logs/) <br> [https://000008.awsstudygroup.com/4-cloud-watch-log/4.2-cloud-watch-logs-insights/](https://000008.awsstudygroup.com/4-cloud-watch-log/4.2-cloud-watch-logs-insights/)                                                                                                                                                                                                                                                                                                                                                                                                                          |
| **4** | Explore CloudWatch Alarm and CloudWatch Dashboard; practice creating threshold-based alerts and dashboards to observe resources.                              | 24/05/2026 | 24/05/2026 | [https://000008.awsstudygroup.com/5-cloud-watch-alarm/](https://000008.awsstudygroup.com/5-cloud-watch-alarm/) <br> [https://000008.awsstudygroup.com/6-cloud-watch-dashboard/](https://000008.awsstudygroup.com/6-cloud-watch-dashboard/) <br> [https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Alarms.html](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Alarms.html) <br> [https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Dashboards.html](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Dashboards.html)                                                                                                                                                                                                                                                                            |
| **5** | Explore AWS CloudTrail, how it records AWS API activity; compare CloudWatch and CloudTrail, consolidate knowledge, and note common errors.                  | 25/05/2026 | 25/05/2026 | [https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-user-guide.html](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-user-guide.html) <br> [https://aws.amazon.com/cloudtrail/](https://aws.amazon.com/cloudtrail/) <br> [https://docs.aws.amazon.com/decision-guides/latest/cloudtrail-or-cloudwatch/cloudtrail-or-cloudwatch.html](https://docs.aws.amazon.com/decision-guides/latest/cloudtrail-or-cloudwatch/cloudtrail-or-cloudwatch.html) <br> [https://cloudjourney.awsstudygroup.com/3-optimize/](https://cloudjourney.awsstudygroup.com/3-optimize/)                                                                                                                                                                                                                                      |

---

## Week 9 Achievements

### Overview

This week, I explored the services for **monitoring and recording system activity on AWS**. Amazon CloudWatch is used to collect metrics, logs, alarms, and dashboards; meanwhile AWS CloudTrail focuses on recording user activity and API calls in the AWS account. According to AWS documentation, CloudWatch serves performance and operational-state monitoring, while CloudTrail serves auditing, security, and activity-history review. ([AWS Documentation][2])

### Knowledge gained

After completing week 9, I have understood:

* The role of **monitoring** and **logging** in operating systems on AWS.
* That **Amazon CloudWatch** is used to monitor AWS resources and applications through metrics, logs, alarms, and dashboards.
* That **CloudWatch Metrics** are system performance measurement data, such as CPU Utilization, Network In/Out, Disk Read/Write, or custom metrics. AWS states that CloudWatch collects metrics from services like EC2, EBS, and RDS, as well as custom metrics sent by users. ([AWS Documentation][3])
* That **CloudWatch Logs** are used to store, search, and analyze system logs.
* That **CloudWatch Logs Insights** supports querying logs to find errors or analyze events.
* That **CloudWatch Alarm** is used to create alerts when a metric crosses a configured threshold.
* That **CloudWatch Dashboard** displays multiple metrics and alarms on one observation interface. ([AWS Documentation][4])
* That **AWS CloudTrail** records activity in the AWS account, including API calls and actions performed by a user, role, or AWS service. ([AWS Documentation][5])
* The distinction that CloudWatch is mostly used for **performance monitoring**, while CloudTrail is mostly used for **auditing and security activity review**.

---

## Practice

During the learning and exploration process, I was able to:

* Access the Amazon CloudWatch service on the AWS Management Console.
* View basic metrics of an EC2 Instance.
* Learn how to filter metrics by namespace and dimension.
* Explore Log Group and Log Stream in CloudWatch Logs.
* Learn how to use CloudWatch Logs Insights to query logs.
* Create a CloudWatch Alarm to alert when a metric exceeds a threshold.
* Learn how to create a CloudWatch Dashboard to observe multiple metrics at once.
* Access the AWS CloudTrail service to view Event History.
* Learn how CloudTrail records actions such as creating, modifying, and deleting AWS resources.
* Note common errors such as missing logs due to unconfigured agent, missing IAM permissions, wrong Region, metrics with no data yet, or an alarm with an unsuitable threshold.

---

## Comparison of CloudWatch and CloudTrail

| Criteria           | Amazon CloudWatch                                   | AWS CloudTrail                                |
| ------------------ | --------------------------------------------------- | --------------------------------------------- |
| **Main purpose**   | Monitor performance and system state                | Record activity history and API calls        |
| **Tracked data**   | Metric, log, alarm, dashboard                       | Event, API call, user activity                |
| **When to use**    | When tracking CPU, RAM, app errors, system logs     | When you need to know who did what in the account |
| **Troubleshooting**| Find performance, app, or resource errors           | Review change history, user actions           |
| **Example**        | Alert when EC2 CPU exceeds 80%                      | Check which user deleted an AWS resource      |

---

## Week 9 Summary

**Week 9:** Explore CloudWatch, CloudTrail, logs, metrics, alarms, and system monitoring. Understand how to monitor AWS resource performance, inspect logs, create alerts, build dashboards, and use CloudTrail to review activity history in the AWS account.

[1]: https://cloudjourney.awsstudygroup.com/3-optimize/?utm_source=chatgpt.com "Optimizing the system - The First Cloud Journey"
[2]: https://docs.aws.amazon.com/decision-guides/latest/cloudtrail-or-cloudwatch/cloudtrail-or-cloudwatch.html?utm_source=chatgpt.com "AWS CloudTrail or Amazon CloudWatch?"
[3]: https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/working_with_metrics.html?utm_source=chatgpt.com "Metrics in Amazon CloudWatch"
[4]: https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Dashboards.html?utm_source=chatgpt.com "Using Amazon CloudWatch dashboards - AWS Documentation"
[5]: https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-user-guide.html?utm_source=chatgpt.com "What Is AWS CloudTrail? - AWS CloudTrail"
