---
title: "Triển khai Backend & API Gateway"
date: 2026-07-10
weight: 4
chapter: false
---

Trong phần này, chúng ta sẽ tiến hành đóng gói và triển khai mã nguồn backend (Node.js) lên **AWS Lambda** và thiết lập **Amazon API Gateway** làm cổng định tuyến API bảo mật tích hợp với bộ xác thực **Cognito Authorizer**.

---
### Bước 1: Tạo IAM Role cho Lambda

Trước khi deploy Lambda, chúng ta cần một IAM Role để cấp các quyền tối thiểu cần thiết cho các hàm chạy logic backend:
- Đọc/Ghi dữ liệu trên 5 bảng **DynamoDB**.
- Tạo Presigned URL và ghi file lên **S3 Bucket**.
- Gửi message vào hàng đợi **SQS**.
- Gửi email thông báo qua **Amazon SES**.
- Ghi logs ra **CloudWatch Logs**.

Bạn có thể tự tạo IAM Role tên là `student-portal-lambda` thông qua IAM Console hoặc chạy script hạ tầng để tạo tự động.

Sau khi tạo xong, hãy lấy ARN của IAM Role:
```text
LAMBDA_ROLE_ARN=arn:aws:iam::<ACCOUNT_ID>:role/student-portal-lambda
```

---
### Bước 2: Triển khai các hàm Lambda (Deploy Lambdas)

Hệ thống bao gồm 21 hàm Lambda thực thi các API nghiệp vụ riêng biệt (CRUD sinh viên, giáo viên, điểm số, tạo URL upload tài liệu và tiến trình worker ngầm gửi email).

Để triển khai toàn bộ các hàm này lên AWS Lambda, hãy xuất các biến môi trường cấu hình và chạy script deploy:

```bash
# 1. Cấu hình các biến môi trường
export LAMBDA_ROLE_ARN="arn:aws:iam::<ACCOUNT_ID>:role/student-portal-lambda"
export DOCUMENTS_BUCKET="student-documents-<yourname>"
export NOTIFICATION_QUEUE_URL="https://sqs.us-east-1.amazonaws.com/<ACCOUNT_ID>/student-notifications"
export FROM_EMAIL="your-verified-email@example.com"

# 2. Chạy script đóng gói và deploy
bash scripts/deploy-lambdas.sh us-east-1
```

> [!TIP]
> Hãy đảm bảo email cấu hình ở `FROM_EMAIL` đã được **Verify** thành công trong dịch vụ **Amazon SES** (ở chế độ Sandbox, cả email gửi và email nhận đều phải được verify trước khi có thể gửi mail thành công).

![AWS Lambda Console](/images/5-Workshop/student-portal/lambda.png)

---
### Bước 3: Triển khai và Cấu hình API Gateway

Để Frontend có thể giao tiếp với các hàm Lambda, chúng ta cần dựng một REST API Gateway để ánh xạ các URL HTTP Endpoint tới từng Lambda function tương ứng và cấu hình xác thực bảo mật.

Chạy script deploy API Gateway:

```bash
# Thiết lập biến User Pool ID đã lưu từ phần trước
export USER_POOL_ID="us-east-1_xxxxxxxxx"

# Chạy script tự động dựng API
bash scripts/deploy-apigateway.sh us-east-1
```

Script này sẽ tự động tạo các tài nguyên trên AWS API Gateway:
1. Tạo một REST API tên `student-portal-api`.
2. Tạo bộ xác thực **Cognito Authorizer** liên kết với User Pool đã cấu hình.
3. Thiết lập các tài nguyên (Resources) và phương thức (Methods):
   - `/students`, `/students/{id}` -> Map tới các Lambda quản lý sinh viên.
   - `/teachers`, `/teachers/{id}` -> Map tới các Lambda quản lý giáo viên.
   - `/grades`, `/grades/{id}` -> Map tới các Lambda quản lý điểm số.
   - `/materials/upload-url`, `/materials/metadata` -> Map tới các Lambda tài liệu học tập.
   - `/documents/upload-url`, `/documents/metadata` -> Map tới các Lambda hồ sơ sinh viên.
4. Áp dụng Cognito Authorizer vào tất cả các method yêu cầu đăng nhập (ví dụ: POST, PUT, DELETE).
5. Bật cấu hình **CORS** cho toàn bộ API để hỗ trợ gọi từ Frontend.
6. Deploy API lên Stage tên là `prod`.

Sau khi chạy xong, hãy copy lại đường dẫn **Invoke URL** hiển thị trên terminal:
```text
https://xxxxxxxxxx.execute-api.us-east-1.amazonaws.com/prod
```
Đây chính là địa chỉ API Gateway Endpoint mà Frontend React của bạn sẽ kết nối tới.

Vào **API Gateway Console → APIs** để xác nhận API `student-portal-api` đã được tạo thành công:
- **Protocol**: REST
- **Endpoint type**: Regional
- **Security policy**: TLS_1_0

![API Gateway Console - Danh sách APIs](/images/5-Workshop/student-portal/api-gateway-list.png)

![API Gateway Overview](/images/5-Workshop/student-portal/api-gateway-1.png)
![API Gateway Details](/images/5-Workshop/student-portal/api-gateway-2.png)
