---
title: "Bản đề xuất"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# TSL-SignMap

## Hệ thống quản lý vị trí biển báo giao thông dựa trên cộng đồng và AI

### 1. Tóm tắt điều hành

TSL-SignMap là hệ thống quản lý biển báo giao thông kết hợp crowdsourcing, AI detection (YOLO), và bản đồ thời gian thực. Hệ thống sử dụng kiến trúc serverless trên AWS, tích hợp mobile app và OpenStreetMap để tạo cơ sở dữ liệu biển báo chính xác, cập nhật liên tục.

### 2. Tuyên bố vấn đề

**Vấn đề hiện tại**

| Vấn đề | Mô tả |
|--------|-------|
| Dữ liệu biển báo lỗi thời | Biển báo thay đổi nhưng bản đồ không cập nhật kịp thời |
| Chi phí khảo sát cao | Khảo sát thủ công tốn thời gian và nhân lực |
| Thiếu tính cộng đồng | Người dân không có kênh đóng góp thông tin |
| Độ chính xác thấp | Xác minh biển báo thủ công dễ sai sót |

**Giải pháp TSL-SignMap**

| Tính năng | Công nghệ |
|-----------|-----------|
| Crowdsourcing | Mobile app cho phép người dùng báo cáo biển báo |
| AI Detection | YOLO model tự động phát hiện và phân loại biển báo |
| Voting System | Cộng đồng xác minh tính chính xác của đóng góp |
| Real-time Map | Tích hợp OpenStreetMap hiển thị biển báo cập nhật |
| Reward System | TSL Coin khuyến khích người dùng tham gia |

**Lợi ích**

| Lợi ích | Chi tiết |
|---------|----------|
| Dữ liệu cập nhật | Biển báo được cập nhật liên tục từ cộng đồng |
| Chi phí thấp | Serverless giảm chi phí infrastructure |
| Độ chính xác cao | AI + voting system đảm bảo chất lượng dữ liệu |
| Khả năng mở rộng | Auto-scaling xử lý traffic tăng đột biến |
| Bảo mật | Multi-layer security bảo vệ dữ liệu người dùng |




### 3. Kiến trúc giải pháp

TSL-SignMap sử dụng kiến trúc serverless, event-driven trên AWS, kết hợp mobile app và AI model để xây dựng hệ thống quản lý biển báo thời gian thực.

![TSL-SignMap Architecture](/images/2-Proposal/aws.jpg)

**Dịch vụ AWS sử dụng**

| Dịch vụ | Vai trò |
|---------|---------|
| Amazon Cognito | Xác thực người dùng, quản lý JWT token |
| Amazon API Gateway | REST API endpoint cho mobile app |
| AWS Lambda | Xử lý logic backend, AI inference |
| Amazon DynamoDB | Lưu trữ thông tin biển báo, user data |
| Amazon S3 | Lưu ảnh biển báo, YOLO model |
| Amazon SageMaker | Training và hosting YOLO model |
| Amazon Location Service | Xử lý geospatial data, tích hợp bản đồ |
| Amazon SQS | Queue xử lý ảnh bất đồng bộ |
| Amazon SNS | Thông báo cho người dùng |
| Amazon CloudWatch | Monitoring và logging |
| AWS Amplify | Deploy mobile app backend |

**Thiết kế thành phần**

| Component | Chi tiết kỹ thuật |
|-----------|-------------------|
| Mobile App | React Native/Flutter kết nối API Gateway |
| Authentication | Cognito User Pool với MFA |
| API Layer | API Gateway + Lambda authorizer |
| AI Detection | Lambda invoke SageMaker endpoint (YOLO) |
| Database | DynamoDB: SignID (PK), Location (GSI) |
| Image Storage | S3 với presigned URL upload |
| Voting System | Lambda + DynamoDB transactions |
| Coin System | DynamoDB table tracks user balance |
| Map Integration | Location Service + OpenStreetMap API |
| Notifications | SNS push notifications via mobile app |




### 4. Triển khai kỹ thuật

**Yêu cầu kỹ thuật**

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




### 5. Lộ trình & Mốc triển khai

| Giai đoạn | Công việc | Thời gian |
|-----------|-----------|-----------|
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

### 6. Ước tính ngân sách

**Chi phí hạ tầng AWS (ước tính/tháng)**

| Dịch vụ AWS | Chi phí ước tính | Ghi chú |
|-------------|------------------|---------|
| Amazon Cognito | $0–$5 | Miễn phí 50k MAU đầu tiên |
| Amazon API Gateway | $3–$10 | ~1M requests/tháng |
| AWS Lambda | $5–$20 | Compute + AI inference |
| Amazon DynamoDB | $10–$30 | On-Demand mode |
| Amazon S3 | $5–$15 | Image storage + model |
| Amazon SageMaker | $20–$50 | YOLO endpoint hosting |
| Amazon Location Service | $5–$15 | Geospatial queries |
| Amazon SQS | $0–$3 | Queue processing |
| Amazon SNS | $0–$5 | Push notifications |
| Amazon CloudWatch | $3–$10 | Logs + metrics |
| **Tổng** | **$51–$163** | Tùy số người dùng và traffic |

**Mô hình kinh tế TSL Coin**

| Hành động | Coin |
|-----------|------|
| Đăng ký mới | +20 Coin |
| Gửi đóng góp biển báo | -5 Coin |
| Đóng góp được duyệt | +10 Coin |
| Vote chính xác | +1 Coin (max 5/ngày) |
| Xem bản đồ | -2 Coin/ngày |
| Nạp tiền | $1 = 10 Coin |

### 7. Đánh giá rủi ro

| Loại rủi ro | Mô tả | Mức độ | Biện pháp giảm thiểu |
|-------------|-------|--------|---------------------|
| **AI Accuracy** | YOLO model nhận diện sai biển báo | Cao | Training với dataset lớn, voting system xác minh |
| **Spam/Abuse** | Người dùng spam đóng góp giả | Trung bình | Rate limiting, reputation system, coin cost |
| **Data Privacy** | Ảnh chụp có thể chứa thông tin cá nhân | Cao | Xóa EXIF metadata, blur faces/license plates |
| **Geolocation Accuracy** | GPS không chính xác ở vùng tín hiệu yếu | Trung bình | Cho phép manual adjustment, crowd verification |
| **Cost Overrun** | SageMaker endpoint có thể tốn kém | Trung bình | Use Lambda cold start, batch processing |
| **Security** | API exposure, unauthorized access | Cao | Cognito authorizer, IAM roles, encryption |
| **Voting Manipulation** | Người dùng tạo nhiều account để vote | Trung bình | Require minimum activity, weight by reputation |
| **Scalability** | Traffic spike khi launch | Thấp | Serverless auto-scaling, DynamoDB on-demand |  
  
### 8. Kết quả kỳ vọng

**Mục tiêu dự án**

| Mục tiêu | Kết quả |
|----------|---------|
| Dữ liệu biển báo | Database 10,000+ biển báo với độ chính xác >90% |
| Người dùng | 1,000+ active users trong 3 tháng đầu |
| AI Detection | YOLO model đạt 85%+ accuracy |
| Response Time | API latency <500ms cho 95% requests |
| Uptime | 99.5%+ availability |
| Cost | Chi phí duy trì <$200/tháng với 5k users |

**Giá trị mang lại**

- **Cho người lái xe**: Bản đồ biển báo cập nhật giúp lái xe an toàn hơn
- **Cho cộng đồng**: Kênh đóng góp cải thiện hạ tầng giao thông
- **Cho cơ quan quản lý**: Dữ liệu real-time về tình trạng biển báo
- **Về kỹ thuật**: Kinh nghiệm xây dựng hệ thống serverless + AI + mobile

**Khả năng mở rộng**

- Tích hợp với navigation apps (Google Maps, Waze)
- Mở rộng sang các loại infrastructure khác (streetlights, road damage)
- API cho third-party developers
- Dashboard analytics cho traffic authorities
- Integration với autonomous vehicle systems
