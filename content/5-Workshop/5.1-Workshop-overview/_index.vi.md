---
title : "Giới thiệu"
date : 2026 
weight : 1
chapter : false
pre : " <b> 5.1. </b> "
---

### TSL-SignMap là gì?

**TSL-SignMap** là hệ thống quản lý vị trí biển báo giao thông dựa trên cộng đồng, tích hợp AI detection và voting system, được xây dựng trên kiến trúc serverless AWS.

**Đặc điểm:**
- Crowdsourcing từ cộng đồng
- AI Detection với YOLO model
- Voting system để xác minh dữ liệu
- Real-time map integration
- Scalable và cost-effective

**Ba nhóm người dùng:**

| Vai trò | Quyền hạn | Chức năng chính |
|---------|-----------|-----------------|
| **Admin** | Full access | Quản lý users, approve/reject signs, monitor system |
| **Contributors** | Submit & vote | Báo cáo biển báo mới, vote cho submissions, earn coins |
| **Drivers** | View only | Xem bản đồ biển báo, tra cứu location |

---

### Tổng quan Workshop

Workshop này hướng dẫn triển khai TSL-SignMap hoàn chỉnh trên AWS trong 6 bước:

#### Bước 1: Infrastructure Setup
Tạo DynamoDB tables, S3 buckets, Cognito User Pool cho authentication.

#### Bước 2: Backend API Development
Deploy Lambda functions, API Gateway endpoints cho submit/vote/query signs.

#### Bước 3: AI Integration
Setup SageMaker endpoint với YOLO model để detect traffic signs tự động.

#### Bước 4: Voting System
Implement reputation-based voting với DynamoDB transactions.

#### Bước 5: Frontend Deployment
Deploy React mobile web app lên S3 + CloudFront.

#### Bước 6: Testing & Monitoring
Test end-to-end workflow và setup CloudWatch monitoring.

---

### Kiến trúc Workshop

Kiến trúc serverless với các thành phần chính:

**Authentication Layer:**
- Cognito User Pool cho login/signup
- JWT token authorization
- MFA optional

**API Layer:**
- API Gateway REST API
- Lambda authorizer
- Rate limiting

**Compute Layer:**
- Lambda functions (Python/Node.js)
- SageMaker endpoint (YOLO)
- Asynchronous processing với SQS

**Data Layer:**
- DynamoDB (signs, users, votes)
- S3 (images, models)
- ElastiCache (optional caching)

**Frontend:**
- React app trên S3
- CloudFront CDN
- Route 53 DNS

**Monitoring:**
- CloudWatch Logs & Metrics
- SNS notifications
- X-Ray tracing

![TSL-SignMap Architecture](/images/5-Workshop/5.1-Workshop-overview/diagram1.png)

---

### Lợi ích Serverless Architecture

| Lợi ích | Mô tả |
|---------|-------|
| **Auto-scaling** | Tự động scale theo traffic, không cần provision |
| **Cost-effective** | Pay-per-use, không có idle cost |
| **High availability** | Multi-AZ deployment tự động |
| **Low maintenance** | AWS quản lý infrastructure |
| **Fast deployment** | CI/CD pipeline đơn giản |

---

### Điều kiện tiên quyết

- Tài khoản AWS có quyền tạo Lambda, DynamoDB, S3, API Gateway
- AWS CLI installed và configured
- Node.js 18+ và Python 3.9+
- Hiểu biết cơ bản về REST API, serverless
- (Optional) Mobile development experience

---

### Nội dung Workshop

1. **[Chuẩn bị](../5.2-prerequisite/)** - Setup AWS environment
2. **[Cơ sở dữ liệu](../5.3-infrastructure-database/)** - DynamoDB tables & S3
3. **[Backend API](../5.4-backend-apigateway/)** - Lambda + API Gateway
4. **[Frontend](../5.5-frontend-deployment/)** - React app deployment
5. **[Testing](../5.6-testing-cleanup/)** - E2E test & cleanup

---

### So sánh Traditional vs Serverless

| Tiêu chí | Traditional | Serverless (TSL-SignMap) |
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

### Ước tính chi phí (5,000 users/month)

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

**Tiết kiệm:**
- Sử dụng Lambda + SQS thay vì SageMaker endpoint 24/7: -$30
- DynamoDB On-Demand thay vì Provisioned: Flexible cost
- CloudFront caching: Giảm Lambda invocations

---

### Thời gian hoàn thành

- **Tổng thời gian:** 90-120 phút
- Bước 1-2 (Infrastructure): 30 phút
- Bước 3 (Backend API): 30 phút
- Bước 4 (Frontend): 20 phút
- Bước 5 (Testing): 20 phút

---

### Kết quả mong đợi

Sau workshop, bạn sẽ có:
- ✅ Hệ thống TSL-SignMap hoàn chỉnh chạy trên AWS
- ✅ Hiểu cách xây dựng serverless crowdsourcing app
- ✅ Kinh nghiệm với Lambda, DynamoDB, API Gateway
- ✅ Mobile backend scalable có thể mở rộng
- ✅ CI/CD pipeline để deploy updates

