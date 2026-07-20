---
title: "Week 12 Worklog"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 1.12. </b> "
---


# Week 12 Worklog

## Week 12 Objectives

In week 12, the main goal was to apply the AWS knowledge learned to the project implementation process. The content focuses on analyzing system requirements, selecting appropriate AWS services, designing the overall architecture, and deploying the main components of the project on the AWS environment.

The project uses services such as **Amazon S3, CloudFront, Cognito, API Gateway, Lambda, DynamoDB, SQS, SNS, SES, and CloudWatch** to build a serverless-oriented system capable of storing data, authenticating users, processing business logic, sending notifications, and monitoring system activity.

---

## Tasks to be carried out this week

| No. | Task                                                                                                                                                                                               | Start Date | Completion Date | Reference Material                                                                                                                                                                                                                                   |
| --- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------- | --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **1** | Analyze project requirements, identify the main system functions, and select appropriate AWS services to deploy.                                                                                  | 05/06/2026 | 05/06/2026      | [https://cloudjourney.awsstudygroup.com/vi/1-explore/](https://cloudjourney.awsstudygroup.com/vi/1-explore/) <br> [https://cloudjourney.awsstudygroup.com/vi/4-modernize/](https://cloudjourney.awsstudygroup.com/vi/4-modernize/)               |
| **2** | Design the overall project architecture, define the flow of operations between users, frontend, API Gateway, Lambda, and the database.                                                            | 06/06/2026 | 10/06/2026      | [https://000079.awsstudygroup.com/vi/](https://000079.awsstudygroup.com/vi/) <br> [https://000078.awsstudygroup.com/vi/](https://000078.awsstudygroup.com/vi/)                                                                                   |
| **3** | Implement storage and user authentication, including storing frontend/files on S3, distributing via CloudFront, and authenticating via Cognito.                                                 | 11/06/2026 | 15/06/2026      | [https://000057.awsstudygroup.com/vi/](https://000057.awsstudygroup.com/vi/) <br> [https://000081.awsstudygroup.com/vi/](https://000081.awsstudygroup.com/vi/)                                                                                   |
| **4** | Build a serverless backend with API Gateway and Lambda, connecting Lambda with DynamoDB to handle adding, editing, deleting, and querying data.                                                   | 16/06/2026 | 20/06/2026      | [https://000079.awsstudygroup.com/vi/](https://000079.awsstudygroup.com/vi/) <br> [https://000078.awsstudygroup.com/vi/](https://000078.awsstudygroup.com/vi/) <br> [https://000053.awsstudygroup.com/vi/](https://000053.awsstudygroup.com/vi/) |
| **5** | Integrate notification/email features with SQS, SNS, SES; monitor system logs and errors with CloudWatch.                                                                                          | 20/06/2026 | 25/06/2026      | [https://000083.awsstudygroup.com/vi/](https://000083.awsstudygroup.com/vi/) <br> [https://aws.amazon.com/vi/ses/](https://aws.amazon.com/vi/ses/) <br> [https://000008.awsstudygroup.com/vi/](https://000008.awsstudygroup.com/vi/)             |

---

## Week 12 Achievements

### Overview

This week, I began applying AWS knowledge to a real project. Instead of exploring individual services separately, I focused on combining AWS services to build a complete system. The services were selected according to their roles in the architecture, including frontend storage, user authentication, API processing, data storage, notifications, and system monitoring.

### Knowledge applied

After completing week 12, I have applied:

* Used **Amazon S3** to store the frontend and system files.
* Used **Amazon CloudFront** to distribute the website, speed up access, and improve performance.
* Used **Amazon Cognito** to authenticate users, supporting registration, login, and token issuance.
* Used **Amazon API Gateway** to create REST APIs and serve as the communication point between frontend and backend.
* Used **AWS Lambda** to process business logic in a serverless model.
* Used **Amazon DynamoDB** to store the system's primary data.
* Used **Amazon SQS** to process asynchronous tasks.
* Used **Amazon SNS** and **Amazon SES** to send notifications or emails to users.
* Used **Amazon CloudWatch** to monitor logs, check errors, and support troubleshooting.

---

## Practice

During the project implementation, I carried out:

* Identified the main functions of the system.
* Selected AWS services appropriate for each function.
* Designed the overall architecture diagram of the project.
* Defined the flow of operations from user to frontend, API, backend, and database.
* Learned how the frontend calls the API through API Gateway.
* Learned how Lambda processes requests and interacts with DynamoDB.
* Learned how Cognito authenticates users before calling the API.
* Learned how SQS supports asynchronous notification/email sending.
* Monitored logs and errors with CloudWatch.
* Noted issues to check such as IAM permissions, CORS errors, API Gateway errors, Lambda timeouts, DynamoDB connection errors, and incurred costs.

---

## Project Service Table

| Service                | Role in the project                          |
| ---------------------- | -------------------------------------------- |
| **Amazon S3**          | Store frontend and system files              |
| **Amazon CloudFront**  | Distribute website, speed up access          |
| **Amazon Cognito**     | Authenticate users, issue login tokens       |
| **Amazon API Gateway** | Create REST APIs for frontend to call backend |
| **AWS Lambda**         | Process business logic                       |
| **Amazon DynamoDB**    | Store the system's primary data              |
| **Amazon SQS**         | Process asynchronous tasks                    |
| **Amazon SNS**         | Send event-based notifications               |
| **Amazon SES**         | Send notification emails to users            |
| **Amazon CloudWatch**  | Write logs, monitor errors, observe system   |

---

## Week 12 Summary

**Week 12:** Applied AWS knowledge to the project, analyzed system requirements, designed the overall architecture, selected appropriate services, and began deploying the main components such as S3, CloudFront, Cognito, API Gateway, Lambda, DynamoDB, SQS, SNS, SES, and CloudWatch.
