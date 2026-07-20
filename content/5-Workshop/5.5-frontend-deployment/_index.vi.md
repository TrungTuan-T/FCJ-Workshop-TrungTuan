---
title: "Triển khai Frontend & Hosting"
date: 2026-07-10
weight: 5
chapter: false
---

Trong phần này, chúng ta sẽ tìm hiểu cấu trúc các trang giao diện của **AWS Student Management Portal**, cấu hình biến môi trường kết nối tới AWS Cognito và API Gateway, chạy thử ứng dụng ở môi trường local và cuối cùng là deploy đóng gói lên dịch vụ **Amazon S3** và phân phối qua **Amazon CloudFront**.

---
### 1. Cấu trúc và Phân quyền các trang Frontend

Mã nguồn Frontend được phát triển bằng **ReactJS** kết hợp công cụ build **Vite**.

#### Cấu trúc thư mục mã nguồn Frontend:
```text
frontend/
├── src/
│   ├── assets/
│   ├── components/
│   │   ├── ConfirmModal.jsx
│   │   ├── GradeForm.jsx
│   │   ├── Layout.jsx
│   │   ├── Navbar.jsx
│   │   ├── ProtectedRoute.jsx
│   │   ├── Sidebar.jsx
│   │   ├── StatusBadge.jsx
│   │   ├── StudentForm.jsx
│   │   └── TeacherForm.jsx
│   ├── config/
│   │   └── awsConfig.js
│   ├── pages/
│   │   ├── admin/
│   │   │   ├── AdminLogs.jsx
│   │   │   ├── AdminRoles.jsx
│   │   │   ├── AdminSettings.jsx
│   │   │   ├── AdminStudentList.jsx
│   │   │   ├── AdminTeacherList.jsx
│   │   │   ├── AdminUserCreate.jsx
│   │   │   ├── AdminUserDetail.jsx
│   │   │   ├── AdminUserEdit.jsx
│   │   │   └── AdminUsers.jsx
│   │   ├── auth/
│   │   │   ├── ForgotPassword.jsx
│   │   │   ├── NewPassword.jsx
│   │   │   ├── ResetPassword.jsx
│   │   │   └── VerifyCode.jsx
│   │   ├── common/
│   │   │   ├── ChangePassword.jsx
│   │   │   ├── Notifications.jsx
│   │   │   ├── Profile.jsx
│   │   │   └── ProfileEdit.jsx
│   │   ├── errors/
│   │   │   ├── Forbidden.jsx
│   │   │   └── NotFound.jsx
│   │   ├── grades/
│   │   │   ├── GradeCreate.jsx
│   │   │   ├── GradeDetail.jsx
│   │   │   ├── GradeEdit.jsx
│   │   │   ├── GradeList.jsx
│   │   │   └── TeacherGrades.jsx
│   │   ├── materials/
│   │   │   ├── MaterialDetail.jsx
│   │   │   ├── MaterialEdit.jsx
│   │   │   ├── StudentMaterials.jsx
│   │   │   └── UploadMaterial.jsx
│   │   ├── students/
│   │   │   ├── StudentCreate.jsx
│   │   │   ├── StudentDetail.jsx
│   │   │   ├── StudentDocuments.jsx
│   │   │   ├── StudentEdit.jsx
│   │   │   ├── StudentList.jsx
│   │   │   └── UploadDocument.jsx
│   │   ├── teachers/
│   │   │   ├── ClassDetail.jsx
│   │   │   ├── ClassList.jsx
│   │   │   ├── TeacherCreate.jsx
│   │   │   ├── TeacherDetail.jsx
│   │   │   ├── TeacherEdit.jsx
│   │   │   └── TeacherList.jsx
│   │   ├── Dashboard.jsx
│   │   ├── Login.jsx
│   │   └── Welcome.jsx
│   ├── services/
│   │   ├── adminService.js
│   │   ├── api.js
│   │   ├── authService.js
│   │   ├── classService.js
│   │   ├── documentService.js
│   │   ├── gradeService.js
│   │   ├── materialService.js
│   │   ├── studentService.js
│   │   └── teacherService.js
│   ├── styles/
│   ├── utils/
│   ├── App.jsx
│   ├── index.css
│   └── main.jsx
├── .env
├── package.json
└── vite.config.js
```

#### Phân quyền và định tuyến bảo vệ:
- **Định tuyến bảo vệ (Protected Routing)**: Hệ thống sử dụng component `ProtectedRoute` để kiểm tra JWT Token từ Cognito.
- **Phân quyền dựa trên Nhóm (Group-based authorization)**:
  - **Admin/Staff**: Có quyền truy cập đầy đủ tất cả các trang bao gồm Dashboard, quản lý danh sách học sinh/giáo viên/điểm, xem logs hệ thống và cấu hình cài đặt.
  - **Teacher (Giáo viên)**: Chỉ có quyền xem danh sách lớp học được phân công, đăng/cập nhật điểm số cho học sinh và đăng tải tài liệu học tập.
  - **Student (Sinh viên)**: Chỉ có quyền xem hồ sơ cá nhân, xem điểm số của chính mình, tải tài liệu học tập do giáo viên đăng tải.

---
### 2. Cấu hình biến môi trường Frontend

Để ứng dụng React kết nối được tới các dịch vụ AWS, hãy tạo file `.env` trong thư mục `frontend/` của dự án với nội dung cấu hình thực tế sau:

```env
# AWS Cognito Configuration
VITE_COGNITO_USER_POOL_ID=us-east-1_7SwNQ0qYm
VITE_COGNITO_CLIENT_ID=6o5g3hcus9ehbmk90acqeuplau

# API Gateway Endpoint (Đường dẫn Invoke URL của API Gateway đã triển khai)
VITE_API_ENDPOINT=https://9k9i3ukwdh.execute-api.us-east-1.amazonaws.com/prod

# AWS Region
VITE_AWS_REGION=us-east-1

# App Configuration
VITE_APP_NAME=AWS Student Management Portal
VITE_APP_VERSION=1.0.0
VITE_ENABLE_NOTIFICATIONS=true
VITE_ENABLE_FILE_UPLOAD=true
```

> [!IMPORTANT]
> Toàn bộ các biến cấu hình sử dụng trong React thông qua Vite phải bắt đầu bằng tiền tố `VITE_`. Nếu đặt tên biến thiếu tiền tố này, ứng dụng sẽ không đọc được giá trị và không thể kết nối tới các dịch vụ AWS.

---
### 3. Chạy thử nghiệm ở môi trường Local

Sau khi đã hoàn tất cấu hình file `.env`, bạn có thể khởi chạy server phát triển local để kiểm tra giao diện:

```bash
cd frontend
npm run dev
```
Mở trình duyệt và truy cập địa chỉ [http://localhost:3000](http://localhost:3000).

Tại đây, bạn thử nghiệm đăng nhập bằng tài khoản Admin demo đã được tạo tự động:
- **Email**: `admin@example.com`
- **Mật khẩu**: `Abc12345!`

Trong lần đăng nhập đầu tiên, hệ thống sẽ yêu cầu bạn đổi mật khẩu mới để tăng tính bảo mật.

#### Giao diện trang đăng nhập (Login Page):
![Login Page](/images/5-Workshop/student-portal/login_page.png)

#### Giao diện Dashboard quản trị:
![Dashboard Page](/images/5-Workshop/student-portal/dashboard_page.png)

#### Danh sách quản lý sinh viên:
![Students List Page](/images/5-Workshop/student-portal/students_list.png)

---
### 4. Build Production & Deploy lên Amazon S3

Khi ứng dụng đã chạy ổn định ở local, chúng ta tiến hành đóng gói mã nguồn React thành các file HTML/JS/CSS tĩnh để đưa lên AWS hosting.

#### 1. Đóng gói mã nguồn (Build)
```bash
npm run build
```
Lệnh này sẽ tạo ra một thư mục tên là `dist/` chứa toàn bộ code tĩnh của trang web.

#### 2. Tạo S3 Bucket cho Hosting Frontend
Tạo một S3 Bucket mới dùng để lưu trữ các file tĩnh này (tên bucket cũng phải là duy nhất toàn cầu):
```bash
aws s3 mb s3://student-portal-frontend-<yourname> --region us-east-1
```

#### 3. Đồng bộ hóa mã nguồn (Deploy)
Tải toàn bộ thư mục `dist/` lên S3:
```bash
aws s3 sync dist/ s3://student-portal-frontend-<yourname> --delete
```
*(Lệnh này sẽ upload tệp mới lên S3 và xóa các tệp cũ không còn sử dụng trên bucket)*

---
### 5. Phân phối qua Amazon CloudFront (Tùy chọn nâng cao)

Để tối ưu hóa tốc độ tải trang toàn cầu và hỗ trợ giao thức bảo mật HTTPS, chúng ta triển khai một **Amazon CloudFront Distribution** trỏ vào S3 bucket frontend ở trên làm Origin:

1. Truy cập **CloudFront Console** → Chọn **Create distribution**.
2. Chọn **Origin domain** trỏ tới S3 bucket frontend của bạn.
3. Thiết lập **Origin access control (OAC)** để bảo vệ S3 bucket, chỉ cho phép truy cập thông qua CloudFront.
4. Thiết lập **Default root object** là `index.html`.
5. Đợi quá trình phân phối hoàn tất (khoảng 5-10 phút).

Sau khi deploy xong, hệ thống được truy cập qua URL CloudFront:

> 🌐 **Link demo:** [https://d3th0yl82lu593.cloudfront.net](https://d3th0yl82lu593.cloudfront.net)

> [!TIP]
> Để cập nhật bản mới sau khi chỉnh sửa code, chạy lại `npm run build` và sync lên S3. Sau đó tạo **CloudFront Invalidation** để xóa cache cũ:
> ```bash
> # Đồng bộ code mới lên S3
> aws s3 sync dist/ s3://student-portal-frontend-<yourname> --delete
>
> # Xóa cache CloudFront (Distribution ID thực tế)
> aws cloudfront create-invalidation --distribution-id <DISTRIBUTION_ID> --paths "/*"
> ```

---
### 6. Xử lý sự cố thường gặp (Troubleshooting)

#### Lỗi 403 Forbidden hoặc CORS khi gọi API từ Local:
- Kiểm tra file `frontend/.env` đã cấu hình đúng `VITE_API_ENDPOINT` chưa (thiếu dấu `/` ở cuối hoặc sai giao thức `https`).
- Đảm bảo cấu hình CORS trên API Gateway đã cho phép domain local của bạn (`http://localhost:3000`).

#### Đăng nhập thất bại / Báo lỗi kết nối UserPool:
- Kiểm tra `VITE_COGNITO_USER_POOL_ID` và `VITE_COGNITO_CLIENT_ID` đã khớp chính xác với thông số trên AWS Console chưa.
- Giá trị đúng: `us-east-1_7SwNQ0qYm` và `6o5g3hcus9ehbmk90acqeuplau`.

#### AWS CLI báo lỗi Expired Token khi deploy:
- Phiên đăng nhập AWS đã hết hạn. Lấy Access Key mới từ AWS IAM và chạy lại `aws configure`.

---
### 7. Tra cứu thông tin dịch vụ AWS (Khi deploy mới)

#### Lấy Cognito User Pool ID & App Client ID:
1. AWS Console → **Cognito** → **User Pools** → Chọn pool dự án.
2. **UserPool ID**: Hiển thị ở đầu trang (dạng `us-east-1_xxxxxxx`).
3. **App Client ID**: Tab **App integration** → Cuộn xuống **App client list** → Copy Client ID.

#### Lấy API Gateway Invoke URL:
1. AWS Console → **API Gateway** → Chọn `student-portal-api`.
2. Menu trái → **Stages** → Chọn stage `prod`.
3. Copy **Invoke URL** ở đầu trang.

#### Lấy CloudFront Distribution ID & S3 Bucket:
1. AWS Console → **CloudFront** → Tìm distribution của domain `d3th0yl82lu593.cloudfront.net`.
2. **Distribution ID**: `E39TFB7INWHA6Y` (cột ID trong bảng danh sách).
3. **S3 Bucket**: Tab **Origins** → Tên bucket: `student-portal-frontend-147997148454`.
