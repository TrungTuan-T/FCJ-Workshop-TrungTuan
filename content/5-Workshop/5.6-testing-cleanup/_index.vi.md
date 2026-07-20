---
title: "Kiểm thử & Dọn dẹp"
date: 2026-07-10
weight: 6
chapter: false
---

Trong phần cuối của workshop, chúng ta sẽ thực hiện kiểm thử toàn bộ hệ thống API thông qua bộ **Postman Collection**, giám sát hoạt động hệ thống bằng **CloudWatch** và tiến hành dọn dẹp các dịch vụ AWS để tránh phát sinh chi phí.

---
### 1. Kiểm thử API bằng Postman

Dự án cung cấp sẵn một file Postman Collection giúp bạn kiểm thử nhanh chóng các API backend mà không cần qua giao diện Frontend.

#### 1.1. Chuẩn bị Postman
1. Mở ứng dụng **Postman**.
2. Click **Import** → Chọn file `postman/student-management-api.postman_collection.json` từ thư mục dự án.
3. Tạo hoặc cập nhật các biến môi trường trong Postman:
   - `baseUrl`: Đường dẫn Invoke URL của API Gateway (ví dụ: `https://xxxxxxxxxx.execute-api.us-east-1.amazonaws.com/prod`).
   - `idToken`: Mã token JWT lấy được từ Cognito sau khi đăng nhập thành công.

#### 1.2. Lấy JWT Token từ Cognito để kiểm thử
Sau khi người dùng (ví dụ: `admin@example.com`) đăng nhập thông qua Cognito, Cognito sẽ trả về một chuỗi `idToken` (JWT).  
Bạn copy chuỗi này và dán vào trường Bearer Token của Postman để thực hiện các yêu cầu API yêu cầu quyền quản trị (như Tạo sinh viên, Xóa giáo viên...).

---
### 2. Giám sát hệ thống qua CloudWatch

Mỗi khi các hàm Lambda được kích hoạt từ API Gateway hoặc SQS, log của chúng sẽ được ghi nhận tự động tại **Amazon CloudWatch Logs**.

- **Cách kiểm tra log**:
  1. Truy cập **CloudWatch Console** → Chọn **Log groups** ở menu bên trái.
  2. Tìm kiếm nhóm log tương ứng với hàm Lambda bạn muốn kiểm tra (ví dụ: `/aws/lambda/getStudents` hoặc `/aws/lambda/sendEmailWorker`).
  3. Chọn log stream mới nhất để theo dõi chi tiết quá trình xử lý, lỗi phát sinh hoặc mã trạng thái trả về.

---
### 3. Dọn dẹp tài nguyên trên AWS (Cleanup)

> [!CAUTION]
> Để tránh phát sinh chi phí không mong muốn trên tài khoản AWS của bạn, hãy chắc chắn thực hiện đầy đủ các bước dọn dẹp dưới đây sau khi đã hoàn thành buổi học hoặc báo cáo.

Chạy tuần tự các lệnh sau trong Terminal/Command Prompt để xóa toàn bộ các dịch vụ đã tạo:

#### 1. Xóa các hàm Lambda
```bash
for fn in createStudent getStudents getStudentById updateStudent deleteStudent \
          createTeacher getTeachers getTeacherById updateTeacher deleteTeacher \
          createGrade getGrades getGradeById updateGrade deleteGrade \
          docUploadUrl docSaveMetadata materialUploadUrl materialSaveMetadata \
          getMaterials sendEmailWorker; do
  aws lambda delete-function --function-name $fn --region us-east-1
done
```

#### 2. Xóa API Gateway
```bash
aws apigateway delete-rest-api --rest-api-id <API_ID> --region us-east-1
```

#### 3. Xóa Cognito User Pool
```bash
aws cognito-idp delete-user-pool --user-pool-id <USER_POOL_ID> --region us-east-1
```

#### 4. Xóa hàng đợi SQS
```bash
aws sqs delete-queue --queue-url <QUEUE_URL> --region us-east-1
```

#### 5. Xóa các bảng DynamoDB
```bash
for table in Students Teachers Grades Materials Documents; do
  aws dynamodb delete-table --table-name $table --region us-east-1
done
```

#### 6. Xóa các S3 Buckets
Lưu ý: S3 không cho phép xóa bucket nếu bên trong vẫn còn file. Do đó bạn cần xóa sạch các file bên trong trước:
```bash
# Xóa file và xóa bucket tài liệu
aws s3 rm s3://student-documents-<yourname> --recursive
aws s3 rb s3://student-documents-<yourname>

# Xóa file và xóa bucket frontend
aws s3 rm s3://student-portal-frontend-<yourname> --recursive
aws s3 rb s3://student-portal-frontend-<yourname>
```

#### 7. Xóa CloudFront Distribution (Nếu có tạo)
1. Vào CloudFront Console.
2. Chọn Distribution của bạn và click **Disable**.
3. Chờ trạng thái chuyển sang disabled (khoảng 5 phút), sau đó chọn Distribution và click **Delete**.

#### 8. Xóa IAM Role
```bash
aws iam delete-role --role-name student-portal-lambda
```

Chúc mừng bạn đã hoàn thành bài Lab triển khai hệ thống quản lý sinh viên Serverless trên AWS!
