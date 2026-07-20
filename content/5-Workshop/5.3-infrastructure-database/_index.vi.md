---
title: "Cơ sở dữ liệu & Hạ tầng"
date: 2026-07-10
weight: 3
chapter: false
---

Trong phần này, chúng ta sẽ bắt đầu khởi chạy các dịch vụ hạ tầng cốt lõi phục vụ lưu trữ dữ liệu, xác thực người dùng và truyền thông báo, bao gồm: **DynamoDB**, **S3 Bucket**, **SQS Queue** và **Amazon Cognito**.

Dự án cung cấp sẵn một số script giúp tự động hóa quá trình cấu hình này.

---
### Bước 1: Cài đặt thư viện phụ thuộc (npm packages)

Di chuyển vào thư mục dự án trên máy tính của bạn và cài đặt các thư viện cần thiết cho cả backend và frontend:

```bash
# 1. Di chuyển vào thư mục backend và cài đặt thư viện
cd backend
npm install

# 2. Di chuyển vào thư mục frontend và cài đặt thư viện
cd ../frontend
npm install
```

---
### Bước 2: Tạo các bảng DynamoDB

Hệ thống quản lý sinh viên sử dụng **6 bảng DynamoDB** để lưu trữ thông tin nghiệp vụ độc lập, tất cả đều dùng `id (S)` làm Partition Key và chạy ở chế độ **On-Demand (PAY_PER_REQUEST)**:

| Bảng | Partition Key | Mô tả |
| :--- | :--- | :--- |
| **Students** | `id` (String) | Hồ sơ sinh viên (studentId) |
| **Teachers** | `id` (String) | Hồ sơ giáo viên (teacherId) |
| **Classes** | `id` (String) | Thông tin lớp học |
| **Grades** | `id` (String) | Điểm số môn học (`${studentId}-${subject}-${timestamp}`) |
| **Materials** | `id` (String) | Tài liệu học tập của giáo viên (`${type}-${timestamp}`) |
| **Documents** | `id` (String) | Metadata hồ sơ sinh viên (`${studentId}-${timestamp}`) |

Chạy script sau để tự động tạo toàn bộ các bảng trên AWS với chế độ Billing `PAY_PER_REQUEST` (On-Demand):

```bash
cd ..
bash scripts/deploy-dynamodb.sh us-east-1
```
*(Hoặc nếu bạn muốn tạo thủ công, bạn có thể thực hiện lệnh `aws dynamodb create-table` cho từng bảng với các Partition Key tương ứng).*

Sau khi chạy script xong, vào **DynamoDB Console → Tables** để xác nhận 6 bảng đã được tạo với trạng thái **Active**:

![DynamoDB Tables Active](/images/5-Workshop/student-portal/dynamodb-tables.png)

---
### Bước 3: Tạo S3 Document Bucket & Cấu hình CORS

S3 Bucket dùng để lưu trữ file tài liệu thực tế của sinh viên và giáo viên. Do Frontend React (chạy tại trình duyệt) sẽ tải trực tiếp file lên S3 qua **Presigned URL**, chúng ta cần cấu hình **CORS** (Cross-Origin Resource Sharing) để trình duyệt không chặn request.

#### 1. Tạo S3 Bucket lưu tài liệu
Lưu ý: Tên bucket của S3 phải là **duy nhất toàn cầu**. Bạn hãy thay `<yourname>` bằng tên viết tắt hoặc mã số cá nhân của bạn:
```bash
aws s3 mb s3://student-documents-<yourname> --region us-east-1
```

#### 2. Áp dụng cấu hình CORS
Sử dụng file cấu hình `cors.json` có sẵn trong dự án:
```bash
aws s3api put-bucket-cors --bucket student-documents-<yourname> --cors-configuration file://cors.json
```
Nội dung file `cors.json` cho phép các HTTP Methods như `PUT` và `POST` từ mọi nguồn (`*`):
```json
{
  "CORSRules": [
    {
      "AllowedHeaders": ["*"],
      "AllowedMethods": ["GET", "PUT", "POST", "DELETE", "HEAD"],
      "AllowedOrigins": ["*"],
      "ExposeHeaders": ["ETag"]
    }
  ]
}
```

---
### Bước 4: Tạo SQS Queue cho thông báo

Tạo hàng đợi hàng thông báo SQS để trung chuyển sự kiện gửi email từ Lambda nghiệp vụ sang Lambda Worker gửi email qua SES:
```bash
aws sqs create-queue --queue-name student-notifications --region us-east-1
```
Ghi lại giá trị **Queue URL** trả về (ví dụ: `https://sqs.us-east-1.amazonaws.com/123456789/student-notifications`).

---
### Bước 5: Thiết lập Amazon Cognito User Pool

Amazon Cognito quản lý việc đăng ký, đăng nhập và cấp mã Token bảo mật JWT để xác thực các request gửi lên API Gateway.

Chạy script cài đặt Cognito tự động:
```bash
bash scripts/setup-cognito.sh us-east-1
```

Script này sẽ thực hiện:
1. Tạo một Cognito User Pool tên `student-portal-user-pool`.
2. Tạo App Client để tích hợp với Frontend React (không sử dụng Client Secret vì chạy trên Browser).
3. Tạo 3 Nhóm người dùng (Cognito Groups): `Admin`, `Teacher`, và `Student`.
4. Tạo một tài khoản Admin demo mặc định:
   - **Username**: `admin@example.com`
   - **Mật khẩu tạm thời**: `Abc12345!` (yêu cầu đổi mật khẩu trong lần đăng nhập đầu tiên).

**Lưu ý quan trọng**: Sau khi script chạy xong, hãy lưu lại thông tin **UserPoolId** và **AppClientId** hiển thị ở màn hình console để cấu hình cho bước tiếp theo.

![Cognito User Pool](/images/5-Workshop/student-portal/cognito.png)

---
### Bước 6: Xác minh Email với Amazon SES

Dịch vụ **Amazon SES (Simple Email Service)** được sử dụng để gửi email thông báo tự động (thông tin tài khoản mới, cập nhật điểm số) tới sinh viên và giáo viên.

> [!WARNING]
> Mặc định, tài khoản AWS mới sẽ ở chế độ **Sandbox**. Ở chế độ này, bạn chỉ có thể gửi email đến các địa chỉ đã được **verify** trước. Giới hạn: **200 emails/24 giờ**, tốc độ **1 email/giây**.

#### 1. Xác minh địa chỉ email người gửi (Sender Identity)
Truy cập **SES Console → Configuration → Identities → Create identity**, chọn **Email address** và nhập email bạn muốn dùng làm người gửi:
```bash
aws ses verify-email-identity --email-address your-sender@example.com --region us-east-1
```
Kiểm tra hộp thư đến và nhấn vào link xác nhận từ AWS.

#### 2. Xác minh địa chỉ email người nhận (khi còn ở Sandbox)
Tương tự, xác minh cả email người nhận (dùng cho việc test):
```bash
aws ses verify-email-identity --email-address recipient@example.com --region us-east-1
```

#### 3. Xác nhận trạng thái SES Account
Vào **SES Console → Account dashboard** để kiểm tra:
- **Region**: US East (N. Virginia)
- **Status**: ✅ Healthy
- **Daily sending quota**: 200 emails/24-hour period
- **Maximum send rate**: 1 email per second

![Amazon SES Account Dashboard](/images/5-Workshop/student-portal/ses-dashboard.png)

> [!TIP]
> Sau khi hoàn thành workshop, nếu muốn gửi email thực tế không bị giới hạn, bạn có thể yêu cầu AWS **chuyển ra khỏi Sandbox** bằng cách submit một support request tại SES Console → Account dashboard → **Request production access**.
