---
title: "Proposal"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# TSL-SignMap

## Community-Driven Traffic Sign Location Management System with AI

### 1. Executive Summary

TSL-SignMap is a traffic sign management system combining crowdsourcing, AI detection (YOLO), and real-time mapping. The system uses serverless architecture on AWS, integrating mobile apps and OpenStreetMap to create an accurate, continuously updated traffic sign database.

### 2. Problem Statement

**Current Problems**

| Problem | Description |
|---------|-------------|
| Outdated sign data | Traffic signs change but maps don't update in time |
| High survey costs | Manual surveys are time and labor intensive |
| Lack of community involvement | People have no channel to contribute information |
| Low accuracy | Manual sign verification is error-prone |

**TSL-SignMap Solution**

| Feature | Technology |
|---------|------------|
| Crowdsourcing | Mobile app allows users to report signs |
| AI Detection | YOLO model automatically detects and classifies signs |
| Voting System | Community verifies contribution accuracy |
| Real-time Map | OpenStreetMap integration displays updated signs |
| Reward System | TSL Coin incentivizes user participation |

**Benefits**

| Benefit | Details |
|---------|---------|
| Updated data | Signs continuously updated by community |
| Low cost | Serverless reduces infrastructure costs |
| High accuracy | AI + voting system ensures data quality |
| Scalability | Auto-scaling handles traffic spikes |
| Security | Multi-layer security protects user data |



### 3. Solution Architecture

TSL-SignMap uses serverless, event-driven architecture on AWS, combining mobile apps and AI models to build a real-time traffic sign management system.

![TSL-SignMap Architecture](/images/2-Proposal/aws.jpg)

**AWS Services Used**

| Service | Role |
|---------|------|
| Amazon Cognito | User authentication, JWT token management |
| Amazon API Gateway | REST API endpoints for mobile app |
| AWS Lambda | Backend logic processing, AI inference |
| Amazon DynamoDB | Store traffic sign info, user data |
| Amazon S3 | Store sign images, YOLO models |
| Amazon SageMaker | Train and host YOLO model |
| Amazon Location Service | Geospatial data processing, map integration |
| Amazon SQS | Asynchronous image processing queue |
| Amazon SNS | User notifications |
| Amazon CloudWatch | Monitoring and logging |
| AWS Amplify | Deploy mobile app backend |

**Component Design**

| Component | Technical Details |
|-----------|-------------------|
| Mobile App | React Native/Flutter connected to API Gateway |
| Authentication | Cognito User Pool with MFA |
| API Layer | API Gateway + Lambda authorizer |
| AI Detection | Lambda invokes SageMaker endpoint (YOLO) |
| Database | DynamoDB: SignID (PK), Location (GSI) |
| Image Storage | S3 with presigned URL upload |
| Voting System | Lambda + DynamoDB transactions |
| Coin System | DynamoDB table tracks user balance |
| Map Integration | Location Service + OpenStreetMap API |
| Notifications | SNS push notifications via mobile app |



### 4. Technical Implementation

**Technical Requirements**

| Layer | AWS Services |
|-------|--------------|
| Mobile App | AWS Amplify, React Native/Flutter |
| Authentication | Amazon Cognito User Pool |
| API Gateway | Amazon API Gateway REST API |
| Compute | AWS Lambda (Python/Node.js) |
| AI/ML | Amazon SageMaker (YOLO model) |
| Database | Amazon DynamoDB (On-Demand) |
| Storage | Amazon S3 (images, models) |
| Geospatial | Amazon Location Service |
| Queue | Amazon SQS (image processing) |
| Notifications | Amazon SNS (push notifications) |
| Monitoring | Amazon CloudWatch |
| Security | AWS KMS, IAM Roles |

**Data Model (DynamoDB)**

```
Table: TrafficSigns
- SignID (PK)
- Location (lat, long) - GSI
- SignType (warning, regulatory, information)
- ImageURL (S3 path)
- Status (pending, approved, rejected)
- SubmittedBy (UserID)
- Votes (upvotes, downvotes)
- CreatedAt, UpdatedAt

Table: Users
- UserID (PK)
- Email
- CoinBalance
- ReputationScore
- SubmissionsCount
- VotesCount

Table: Votes
- VoteID (PK)
- SignID-UserID (GSI)
- VoteType (upvote, downvote)
- Weight (based on reputation)
- Timestamp
```



### 5. Roadmap & Implementation Milestones

| Phase | Tasks | Timeline |
|-------|-------|----------|
| **Phase 1: Infrastructure** | Setup AWS account, VPC, IAM roles | 1 week |
| | Deploy Cognito User Pool | |
| | Setup DynamoDB tables | |
| | Configure S3 buckets | |
| **Phase 2: Backend API** | Create Lambda functions | 2 weeks |
| | Setup API Gateway endpoints | |
| | Implement authentication flow | |
| | Deploy SQS queues | |
| **Phase 3: AI Integration** | Train YOLO model on traffic signs | 2 weeks |
| | Deploy model to SageMaker | |
| | Create Lambda for AI inference | |
| | Test detection accuracy | |
| **Phase 4: Mobile App** | Develop React Native app | 3 weeks |
| | Integrate with API Gateway | |
| | Implement map view (Location Service) | |
| | Add camera and image upload | |
| **Phase 5: Voting & Coins** | Implement voting system | 1 week |
| | Create coin transaction logic | |
| | Add reputation calculation | |
| **Phase 6: Testing** | Integration testing | 1 week |
| | Load testing with traffic simulation | |
| | Security audit | |
| **Phase 7: Launch** | Deploy to production | 1 week |
| | Monitor with CloudWatch | |
| | Gather user feedback | |

### 6. Budget Estimation

**AWS Infrastructure Costs (estimated/month)**

| AWS Service | Estimated Cost | Notes |
|-------------|----------------|-------|
| Amazon Cognito | $0–$5 | First 50k MAU free |
| Amazon API Gateway | $3–$10 | ~1M requests/month |
| AWS Lambda | $5–$20 | Compute + AI inference |
| Amazon DynamoDB | $10–$30 | On-Demand mode |
| Amazon S3 | $5–$15 | Image storage + model |
| Amazon SageMaker | $20–$50 | YOLO endpoint hosting |
| Amazon Location Service | $5–$15 | Geospatial queries |
| Amazon SQS | $0–$3 | Queue processing |
| Amazon SNS | $0–$5 | Push notifications |
| Amazon CloudWatch | $3–$10 | Logs + metrics |
| **Total** | **$51–$163** | Depends on users and traffic |

**TSL Coin Economics Model**

| Action | Coins |
|--------|-------|
| New registration | +20 Coins |
| Submit sign contribution | -5 Coins |
| Contribution approved | +10 Coins |
| Accurate vote | +1 Coin (max 5/day) |
| View map | -2 Coins/day |
| Top up | $1 = 10 Coins |

### 7. Risk Assessment

| Risk Type | Description | Level | Mitigation Measures |
|-----------|-------------|-------|-------------------|
| **AI Accuracy** | YOLO model misidentifies traffic signs | High | Train with large dataset, voting system verification |
| **Spam/Abuse** | Users spam fake contributions | Medium | Rate limiting, reputation system, coin cost |
| **Data Privacy** | Photos may contain personal information | High | Remove EXIF metadata, blur faces/license plates |
| **Geolocation Accuracy** | GPS inaccurate in weak signal areas | Medium | Allow manual adjustment, crowd verification |
| **Cost Overrun** | SageMaker endpoint can be expensive | Medium | Use Lambda cold start, batch processing |
| **Security** | API exposure, unauthorized access | High | Cognito authorizer, IAM roles, encryption |
| **Voting Manipulation** | Users create multiple accounts to vote | Medium | Require minimum activity, weight by reputation |
| **Scalability** | Traffic spike at launch | Low | Serverless auto-scaling, DynamoDB on-demand |  
  
### 8. Expected Outcomes

**Project Goals**

| Goal | Result |
|------|--------|
| Traffic sign data | Database of 10,000+ signs with >90% accuracy |
| Users | 1,000+ active users in first 3 months |
| AI Detection | YOLO model achieves 85%+ accuracy |
| Response Time | API latency <500ms for 95% of requests |
| Uptime | 99.5%+ availability |
| Cost | Maintenance cost <$200/month with 5k users |

**Value Delivered**

- **For drivers**: Updated sign maps help drivers navigate safely
- **For community**: Channel to contribute to traffic infrastructure improvements
- **For authorities**: Real-time data on traffic sign conditions
- **Technical learning**: Experience building serverless + AI + mobile systems

**Scalability Options**

- Integration with navigation apps (Google Maps, Waze)
- Expand to other infrastructure types (streetlights, road damage)
- API for third-party developers
- Analytics dashboard for traffic authorities
- Integration with autonomous vehicle systems
