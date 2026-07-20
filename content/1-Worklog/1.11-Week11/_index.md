---
title: "Week 11 Worklog"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 1.11. </b> "
---


# Week 11 Worklog

## Week 11 Objectives

In week 11, the main goal was to explore the **serverless computing** content group on AWS. This is part of the **Modernize / Modernizing Applications** group in the AWS Cloud Journey, focusing on serverless computing, API-first, event-driven systems, user authentication, and asynchronous messaging. ([Cloud Journey][1])

Key topics for the week include:

* Getting an overview of serverless architecture.
* Exploring **AWS Lambda** to process backend logic.
* Exploring **Amazon API Gateway** to create REST APIs.
* Exploring **Amazon Cognito** for user authentication.
* Exploring **Amazon SQS** and **Amazon SNS** for queue and notification processing.
* Exploring **Amazon SES** to send automated emails in applications.
* Combining Lambda, API Gateway, Cognito, SQS, SNS, and SES into complete serverless workflows.

---

## Tasks to be carried out this week

| No. | Task                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | Start Date | Completion Date | Reference Material                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------- | --------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **1** | Get an overview of application modernization on AWS, serverless models, API-first, microservices, and event-driven architecture.                                                                                                                                                                                                                                                                                                                                                                                                                       | 31/05/2026 | 31/05/2026      | [https://cloudjourney.awsstudygroup.com/vi/4-modernize/](https://cloudjourney.awsstudygroup.com/vi/4-modernize/) <br> [https://000078.awsstudygroup.com/vi/](https://000078.awsstudygroup.com/vi/)                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| **2** | Explore AWS Lambda, how to create Lambda Functions, runtime, handler, trigger, IAM Role, and how Lambda processes backend logic without server management.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | 1/06/2026 | 1/06/2026      | [https://000078.awsstudygroup.com/vi/](https://000078.awsstudygroup.com/vi/) <br> [https://000078.awsstudygroup.com/vi/2-xu-ly-va-toi-uu-kich-thuoc-anh-tren-aws/](https://000078.awsstudygroup.com/vi/2-xu-ly-va-toi-uu-kich-thuoc-anh-tren-aws/) <br> [https://000066.awsstudygroup.com/](https://000066.awsstudygroup.com/)                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| **3** | Explore Amazon API Gateway, how to create APIs, method, resource, integrate API Gateway with Lambda, enable CORS, and test APIs with Postman or frontend.                                                                                                                                                                                                        | 02/06/2026 | 02/06/2026      | [https://000079.awsstudygroup.com/vi/](https://000079.awsstudygroup.com/vi/) <br> [https://000079.awsstudygroup.com/vi/4-thiet-lap-api-gateway/](https://000079.awsstudygroup.com/vi/4-thiet-lap-api-gateway/) <br> [https://000066.awsstudygroup.com/](https://000066.awsstudygroup.com/)                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| **4** | Explore Amazon Cognito, User Pool, Identity Pool, registration flow, login flow, token authentication, and integrate Cognito with API/Lambda.                                                                                                                                                                                                          | 03/06/2026 | 03/06/2026      | [https://000081.awsstudygroup.com/vi/](https://000081.awsstudygroup.com/vi/) <br> [https://000081.awsstudygroup.com/vi/2-create-user-pool/](https://000081.awsstudygroup.com/vi/2-create-user-pool/) <br> [https://000081.awsstudygroup.com/vi/3-create-api-and-lambda-function/](https://000081.awsstudygroup.com/vi/3-create-api-and-lambda-function/)                                                                                                                                                                                                                                                                                                                                                                                                           |
| **5** | Explore SQS, SNS, and SES; how to process queues, send notifications, send automated emails, and combine into a complete serverless workflow.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | 04/06/2026 | 04/06/2026      | [https://000083.awsstudygroup.com/vi/](https://000083.awsstudygroup.com/vi/) <br> [https://000083.awsstudygroup.com/vi/2-create-queue-and-sns-topic/](https://000083.awsstudygroup.com/vi/2-create-queue-and-sns-topic/) <br> [https://aws.amazon.com/vi/ses/](https://aws.amazon.com/vi/ses/) <br> [https://docs.aws.amazon.com/ses/latest/dg/Welcome.html](https://docs.aws.amazon.com/ses/latest/dg/Welcome.html) |

---

## Week 11 Achievements

### Overview

This week, I explored **serverless application architecture** on AWS. A basic serverless architecture can use **AWS Lambda** to process logic, **Amazon API Gateway** to receive requests from users, **Amazon Cognito** for authentication, **Amazon DynamoDB/S3** for data storage, and messaging services like **SQS/SNS** for asynchronous processing. AWS Study Group also describes a backend serverless that can use Lambda, API Gateway, S3, DynamoDB, and Cognito to build a complete application. ([000066.awsstudygroup.com][2])

### Knowledge gained

After completing week 11, I have understood:

* Understand **serverless** as a deployment model where developers focus on code and business logic, reducing server management overhead.
* Understand **AWS Lambda** for running backend code based on events, which can be combined with S3, DynamoDB, API Gateway, or other services. The Lambda workshop with S3 and DynamoDB shows that Lambda can process logic, interact with data, and be triggered by events. ([000078.awsstudygroup.com][3])
* Understand **Amazon API Gateway** for creating APIs for frontend or client to call backend Lambda. In the API Gateway workshop, the frontend interacts with the database through Lambda and API Gateway. ([000079.awsstudygroup.com][4])
* Understand **Amazon Cognito** for registration, login, authentication, user management, User Pool and Identity Pool for web or mobile applications. ([000081.awsstudygroup.com][5])
* Understand **Amazon SQS** for creating message queues, helping decouple system components and process asynchronous tasks.
* Understand **Amazon SNS** for sending notifications based on publish/subscribe model, where publishers send messages to topics and subscribers receive messages from topics. ([000083.awsstudygroup.com][6])
* Understand **Amazon SES** as a cloud email service that can be integrated into applications to send transaction emails, notification emails, or automated emails. ([Amazon Web Services, Inc.][7])

---

## Practice

During the learning and exploration process, I was able to:

* Explore the **Modernize Applications** learning path on AWS Cloud Journey.
* Explore serverless architecture with frontend, API Gateway, Lambda, Cognito, and database.
* Learn how to create Lambda Functions and configure IAM Role permissions for Lambda.
* Learn how Lambda interacts with S3 and DynamoDB.
* Learn how to create APIs using Amazon API Gateway.
* Learn how to configure method, resource, CORS and integrate API Gateway with Lambda.
* Learn how to create User Pools in Amazon Cognito.
* Learn the registration, login, and token authentication flows using Cognito.
* Learn how to create SQS Queues and SNS Topics.
* Learn how to send notifications when system events occur.
* Learn Amazon SES and use cases for sending confirmation emails, notification emails, or transaction emails.
* Note common errors such as missing IAM permissions for Lambda, CORS errors when calling APIs, incorrect API Gateway endpoints, invalid Cognito tokens, Lambda timeouts, or not cleaning up resources after practice.

---

## Week 11 Service Summary

| Service                | Main Purpose                     | Role in the serverless model                  |
| ---------------------- | -------------------------------- | --------------------------------------------- |
| **AWS Lambda**         | Run code without server management | Process backend logic                           |
| **Amazon API Gateway** | Create and manage APIs           | Receive requests from frontend/client and call Lambda |
| **Amazon Cognito**     | User authentication and management | Registration, login, token issuance                 |
| **Amazon SQS**         | Message queues                   | Process asynchronous tasks, decouple systems   |
| **Amazon SNS**         | Send notifications by topic      | Send notifications to subscribers               |
| **Amazon SES**         | Send email                       | Send confirmation, notification, transaction emails |

---

## Week 11 Summary

**Week 11:** Explored Lambda, API Gateway, Cognito, SQS, SNS, and SES in serverless architecture. Learned how to build serverless backends without server management, create APIs for frontend, authenticate users, process asynchronous tasks, and send notifications/emails in AWS systems.

[1]: https://cloudjourney.awsstudygroup.com/vi/4-modernize/ "Modernize Applications :: The First Cloud Journey"
[2]: https://000066.awsstudygroup.com/ "Serverless with Lambda, API Gateway and SAM :: SERVERLESS WITH LAMBDA, API GATEWAY AND SAM"
[3]: https://000078.awsstudygroup.com/vi/ "Serverless - Lambda Interaction with S3 and DynamoDB :: AWS System Manager"
[4]: https://000079.awsstudygroup.com/vi/ "Serverless - Build Frontend to Call API Gateway :: Serverless - Build Frontend to call API Gateway"
[5]: https://000081.awsstudygroup.com/vi/ "Serverless - Authentication with Amazon Cognito :: SERVERLESS - XÁC THỰC VỚI AMAZON COGNITO"
[6]: https://000083.awsstudygroup.com/vi/ "Serverless - Order Processing with SQS-SNS :: SERVERLESS - XỬ LÝ ĐƠN HÀNG VỚI SQS-SNS"
[7]: https://aws.amazon.com/vi/ses/?utm_source=chatgpt.com "Amazon Simple Email Service (Amazon SES)"
