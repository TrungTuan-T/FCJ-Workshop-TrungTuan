---
title: "Proposal"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# AWS Student Management Portal

## A Student Management Portal Powered by AWS

### 1. Executive Summary

The AWS Student Management Portal is designed according to a modern cloud model, using serverless architecture, automation, and multi-layer security to improve operational efficiency and information protection.

### 2. Problem Statement

 _Current problem_

  * Traditional monolithic application architectures often face difficulties with product release, causing delayed releases, missed business opportunities, and reduced revenue.
  * The system becomes inefficient when traffic surges suddenly (for example, during course registration or student document submission periods), leading to high 24/7 server maintenance costs while performance remains suboptimal.
  * Manual deployment processes are prone to human error. In addition, weak compliance with security regulations or loose access control can compromise information security and damage system credibility.



_Solution_

  * Build the AWS Student Management Portal by fully transitioning to a modern architecture: split the system into modular components with independent microservices combined with serverless services (AWS Lambda, DynamoDB, and API Gateway).
  * Apply event-driven architecture using Amazon SQS to handle asynchronous tasks and enable flexible communication between services.
  * Implement multi-layer security from the network layer (WAF, CloudFront), identity layer (Cognito), to the data layer (AWS KMS, IAM Roles).
  * Fully automate the software development lifecycle (SDLC) through an automated CI/CD pipeline (CodeCommit → CodePipeline → CodeBuild).



_Benefits and Return on Investment (ROI)_

  * Cost and performance efficiency: Serverless mechanisms eliminate the need to manage physical infrastructure. The system automatically scales immediately when traffic increases and uses a pay-for-value model—paying only when the code runs.
  * Flexibility and resilience: Asynchronous communication via queues creates high loose coupling, reducing backend load and helping ensure the system does not fail during local overload.
  * Absolute information security: Reduce the risk of data leakage through automatic encryption at rest, least-privilege access (IAM Roles), and periodic backup and recovery policies (AWS Backup).
  * Faster product development: Automated CI/CD reduces manual mistakes and shortens the time required to release new versions.



### 3. Solution Architecture

The AWS Student Management Portal is built on cloud-native architecture, combining serverless computing and event-driven design. The entire architecture is divided into independent functional layers, ensuring automatic scalability, cost optimization, and multi-layer security.

![AWS Student Management Portal](/images/2-Proposal/aws.jpg)

_AWS services used_

  * Amazon Route 53: Domain management and DNS resolution service.
  * Amazon CloudFront: Global content distribution network (CDN) that accelerates page loading and caching.
  * AWS WAF: Web application firewall that protects applications from malicious network attacks.
  * Amazon S3: Stores static frontend source code (website hosting) and student document files.
  * Amazon Cognito: Identity management service for user registration/sign-in and JWT token issuance.
  * Amazon API Gateway: Entry point for receiving, routing, and controlling API requests and enforcing rate limiting.
  * AWS Lambda: Serverless compute service that executes backend logic automatically in response to events.
  * Amazon DynamoDB: High-performance NoSQL database for storing detailed student information.
  * Amazon SQS: Message queue that supports receiving and processing asynchronous tasks.
  * Amazon SES: Service for sending automated emails to students and administrators.
  * Amazon CloudWatch: Monitoring tool for system performance, logs, metrics, and alarms.
  * Amazon SNS: Publish/subscribe notification service for sending system alerts to administrators.
  * AWS KMS: Manages and creates encryption keys for stored data.
  * AWS Backup: Centrally manages automated backup and recovery policies.
  * AWS CodeCommit: Secure Git-based source code repository.
  * AWS CodeBuild: Compiles source code, runs tests, and packages artifacts automatically.
  * AWS CodePipeline: Orchestrates the continuous deployment (CI/CD) workflow.



_Component design_

  * Amazon Route 53: Configure A Records (Alias) to direct the domain to CloudFront.
  * Amazon CloudFront: Create a distribution, configure HTTPS, and optimize caching policies for static files.
  * AWS WAF: Attach a Web ACL to CloudFront, enable rules against SQL injection, XSS, and API rate limiting.
  * Amazon S3 (Frontend): Enable Static Website Hosting and configure OAC (Origin Access Control) so that only CloudFront can access content while blocking public access.
  * Amazon S3 (Data Storage): Enable Versioning, configure CORS, and allow secure file uploads through presigned URLs.
  * Amazon Cognito: Create a User Pool to manage student accounts and issue JWT tokens.
  * Amazon API Gateway: Set up REST API endpoints and integrate a Cognito Authorizer to allow or block requests.
  * AWS Lambda: Run backend code (Node.js/Python) and scale automatically based on incoming events.
  * Amazon DynamoDB: Configure On-Demand mode for automatic capacity scaling and set StudentID as the primary key.
  * Amazon SQS and SES: Configure Standard/FIFO queues for asynchronous processing and call the SES API to send emails after proper DKIM/SPF configuration.
  * CloudWatch and SNS: Create metric filters to detect system errors and configure alarms that send alerts through SNS topics.
  * AWS KMS and Backup: Encrypt stored data automatically and set periodic backup policies ready for disaster recovery.
  * CI/CD (Code Suite): Automate build and deployment workflows.



### 4. Technical Implementation

 _Technical requirements_

  * Compute and Logic Layer: AWS Lambda (for asynchronous backend processing and management logic) and Amazon API Gateway.
  * Storage and Database: Amazon DynamoDB (database for storing student information) and Amazon S3 (storage for static frontend assets and student documents).
  * Security and Identity: Amazon Cognito, IAM Roles, and AWS KMS (encryption key management).
  * Observability: Amazon CloudWatch (logs and metrics), CloudWatch Alarms, and Amazon SNS (alert coordination).
  * Data Protection and Resiliency: AWS Backup (periodic backup and disaster recovery policy).
  * CI/CD Automation: AWS CodeCommit (source management), AWS CodeBuild (packaging and testing), and AWS CodePipeline (continuous deployment automation).



### 5. Roadmap and Implementation Milestones

  * Phase 1: Infrastructure setup and data security configuration



Set up the core services and establish multi-layer security mechanisms.

Use AWS KMS to encrypt all data at rest stored in Amazon DynamoDB and Amazon S3.

Set up IAM Roles according to the least privilege principle so each service only has the permissions it needs.

  * Phase 2: Monitoring and operations configuration



Integrate Amazon CloudWatch to centrally collect logs and performance metrics from AWS Lambda and API Gateway.

Set up CloudWatch Alarms to monitor error thresholds such as Lambda crashes or API Gateway overload.

Connect the alerting system with Amazon SNS to send immediate warnings to the operations team when anomalies are detected.

  * Phase 3: Continuous deployment pipeline automation (CI/CD)



Set up a secure Git-based source repository using AWS CodeCommit.

Use AWS CodeBuild to automate compilation, testing, and packaging.

Configure AWS CodePipeline to automatically update the frontend on Amazon S3 and deploy the backend to AWS Lambda whenever new source code is pushed.

  * Phase 4: Backup and periodic recovery setup



Configure AWS Backup centrally to manage backups for DynamoDB and S3.

Set backup and recovery policies to be ready for emergencies and data disasters.

### 6. Budget Estimation

 _Infrastructure costs_

AWS Service| Estimated Cost per Month  
---|---  
Amazon S3 (frontend + documents)| $3–$8  
Amazon CloudFront| $5–$15  
Amazon API Gateway| $3–$8  
AWS Lambda| $2–$10  
Amazon DynamoDB| $10–$25  
Amazon Cognito| $0–$10  
Amazon SQS| $0–$2  
Amazon SES| $0–$10  
Amazon CloudWatch Logs| $2–$8  
  
Total estimate: approximately $25–$96 per month, depending on traffic, storage volume, and number of emails sent.

### 7. Risk Assessment

The main risks when deploying and operating the AWS Student Management Portal are as follows:

Risk Type| Description| Level| Mitigation Measures  
---|---|---|---  
Security| Cognito tokens, IAM roles, or S3 settings may be exploited if they are not properly controlled| High| Use Cognito Authorizer, limit IAM permissions, block public access to buckets, enable CloudFront, and secure environment variables  
Data| Student data and documents may be lost or become inconsistent if the schema changes or processing errors occur| Medium| Use DynamoDB backups, validate input data, and implement logging and retry logic  
Deployment| Incorrect configuration of API Gateway, Lambda, Cognito, or S3 may prevent the system from running properly| Medium| Test each component thoroughly and use dev/test environments before deploying to production  
Operational Cost| The number of Lambda requests, DynamoDB read/write operations, and stored files can cause costs to increase quickly| Medium| Optimize Lambda usage, use DynamoDB On-Demand, and limit logs and stored files  
Operations| Lambda cold starts, timeouts, or SQS/SES failures can affect the user experience| Medium| Configure reasonable timeouts, monitor CloudWatch, and set alerts and retries  
Email Limits| SES in sandbox mode may limit recipients and cause email delivery failures| Medium| Verify senders/recipients before use and check SES and queue worker configuration  
  
### 8. Expected Outcomes

This project is expected to deliver the following key values:

  * Gain a clear understanding of how to build a web application using a serverless model on AWS.
  * Practice integrating AWS services such as S3, CloudFront, Cognito, API Gateway, Lambda, DynamoDB, SQS, SES, and CloudWatch.
  * Create a student management platform that can be scaled for future real-world systems.
  * Support learning, demos, and cloud deployment reporting in a clear and structured way.
  * Create a foundation for more advanced projects such as detailed role-based access control, statistics dashboards, CI/CD, or production deployment.



In practice, the project is expected not only to be a demo interface but also to demonstrate how to design architecture, process data, ensure security, and operate the system on a cloud platform.
