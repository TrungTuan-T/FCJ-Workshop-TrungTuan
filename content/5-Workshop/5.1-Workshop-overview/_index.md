---
title : "Introduction"
date : 2026 
weight : 1
chapter : false
pre : " <b> 5.1. </b> "
---

### What is TSL-SignMap?

**TSL-SignMap** is a community-driven traffic sign location management system with AI detection and voting system, built on AWS serverless architecture.

**Features:**
- Crowdsourcing from community
- AI Detection with YOLO model
- Voting system for data verification
- Real-time map integration
- Scalable and cost-effective

**Three User Groups:**

| Role | Permissions | Main Functions |
|------|-------------|----------------|
| **Admin** | Full access | Manage users, approve/reject signs, monitor system |
| **Contributors** | Submit & vote | Report new signs, vote on submissions, earn coins |
| **Drivers** | View only | View sign map, search locations |

---

### Workshop Overview

This workshop guides you through deploying a complete TSL-SignMap system on AWS in 6 steps:

#### Step 1: Infrastructure Setup
Create DynamoDB tables, S3 buckets, Cognito User Pool for authentication.

#### Step 2: Backend API Development
Deploy Lambda functions, API Gateway endpoints for submit/vote/query signs.

#### Step 3: AI Integration
Setup SageMaker endpoint with YOLO model to automatically detect traffic signs.

#### Step 4: Voting System
Implement reputation-based voting with DynamoDB transactions.

#### Step 5: Frontend Deployment
Deploy React mobile web app to S3 + CloudFront.

#### Step 6: Testing & Monitoring
Test end-to-end workflow and setup CloudWatch monitoring.

---

### Workshop Architecture

Serverless architecture with main components:

**Authentication Layer:**
- Cognito User Pool for login/signup
- JWT token authorization
- Optional MFA

**API Layer:**
- API Gateway REST API
- Lambda authorizer
- Rate limiting

**Compute Layer:**
- Lambda functions (Python/Node.js)
- SageMaker endpoint (YOLO)
- Asynchronous processing with SQS

**Data Layer:**
- DynamoDB (signs, users, votes)
- S3 (images, models)
- ElastiCache (optional caching)

**Frontend:**
- React app on S3
- CloudFront CDN
- Route 53 DNS

**Monitoring:**
- CloudWatch Logs & Metrics
- SNS notifications
- X-Ray tracing

![TSL-SignMap Architecture](/images/5-Workshop/5.1-Workshop-overview/diagram1.png)

---

### Serverless Architecture Benefits

| Benefit | Description |
|---------|-------------|
| **Auto-scaling** | Automatically scales with traffic, no provisioning needed |
| **Cost-effective** | Pay-per-use, no idle costs |
| **High availability** | Automatic multi-AZ deployment |
| **Low maintenance** | AWS manages infrastructure |
| **Fast deployment** | Simple CI/CD pipeline |

---

### Prerequisites

- AWS account with permissions for Lambda, DynamoDB, S3, API Gateway
- AWS CLI installed and configured
- Node.js 18+ and Python 3.9+
- Basic understanding of REST API, serverless
- (Optional) Mobile development experience

---

### Workshop Contents

1. **[Preparation](../5.2-prerequisite/)** - Setup AWS environment
2. **[Database](../5.3-infrastructure-database/)** - DynamoDB tables & S3
3. **[Backend API](../5.4-backend-apigateway/)** - Lambda + API Gateway
4. **[Frontend](../5.5-frontend-deployment/)** - React app deployment
5. **[Testing](../5.6-testing-cleanup/)** - E2E test & cleanup

---

### Traditional vs Serverless Comparison

| Criteria | Traditional | Serverless (TSL-SignMap) |
|----------|-------------|--------------------------|
| **Infrastructure** | EC2, Load Balancer, Auto Scaling | Lambda, API Gateway |
| **Database** | RDS (MySQL/Postgres) | DynamoDB |
| **Scaling** | Manual configuration | Automatic |
| **Cost** | $50-200/month (always on) | $10-50/month (pay per use) |
| **Maintenance** | High (patching, updates) | Low (AWS managed) |
| **Deployment** | Complex (Docker, K8s) | Simple (SAM, Serverless Framework) |

---

### Tech Stack

**Backend:**
- AWS Lambda (Python 3.9)
- Amazon API Gateway
- Amazon DynamoDB
- Amazon S3
- Amazon SageMaker
- Amazon Cognito

**Frontend:**
- React 18+
- AWS Amplify
- Mapbox/OpenStreetMap
- Material-UI

**DevOps:**
- AWS SAM / Serverless Framework
- GitHub Actions
- CloudWatch

---

### Cost Estimation (5,000 users/month)

| Service | Usage | Cost |
|---------|-------|------|
| API Gateway | 5M requests | $17.50 |
| Lambda | 10M invocations | $15 |
| DynamoDB | 5M reads, 1M writes | $7.50 |
| S3 | 100K images, 50GB | $1.50 |
| SageMaker | Endpoint 24/7 | $35 |
| Cognito | 5K MAU | Free |
| CloudFront | 50GB transfer | $4 |
| **Total** | | **~$80.50/month** |

**Cost Savings:**
- Use Lambda + SQS instead of 24/7 SageMaker endpoint: -$30
- DynamoDB On-Demand vs Provisioned: Flexible cost
- CloudFront caching: Reduce Lambda invocations

---

### Time to Complete

- **Total time:** 90-120 minutes
- Steps 1-2 (Infrastructure): 30 minutes
- Step 3 (Backend API): 30 minutes
- Step 4 (Frontend): 20 minutes
- Step 5 (Testing): 20 minutes

---

### Expected Outcomes

After the workshop, you will have:
- ✅ Complete TSL-SignMap system running on AWS
- ✅ Understanding of building serverless crowdsourcing apps
- ✅ Experience with Lambda, DynamoDB, API Gateway
- ✅ Scalable mobile backend
- ✅ CI/CD pipeline for deploying updates

