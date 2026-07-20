---
title: "Các bước chuẩn bị"
date: 2026-07-10
weight: 2
chapter: false
---

Để bắt đầu triển khai dự án **AWS Student Management Portal**, bạn cần chuẩn bị đầy đủ môi trường lập trình và cấu hình thông tin truy cập AWS trên máy tính của mình.

---
### 1. Cài đặt các công cụ yêu cầu

#### 1.1. Node.js & npm (Môi trường Runtime)
Node.js dùng để chạy backend Lambda local (khi cần test) và biên dịch mã nguồn Frontend React.
- **Yêu cầu**: Node.js `>= 18.x`, npm `>= 9.x`.
- **Cách cài đặt**:
  - Truy cập [Node.js Official Website](https://nodejs.org/) và tải về bản LTS mới nhất.
  - Trong quá trình cài đặt trên Windows, đảm bảo đánh dấu tích vào ô **"Add to PATH"**.
- **Kiểm tra**: Mở Terminal/Command Prompt và chạy các lệnh:
  ```bash
  node --version
  npm --version
  ```

#### 1.2. Python (Dùng cho script tự động đóng gói)
Python được sử dụng bởi các script tự động nén mã nguồn trước khi deploy lên AWS Lambda.
- **Yêu cầu**: Python `>= 3.8`.
- **Cách cài đặt**:
  - Tải bản cài đặt từ trang chủ [Python](https://www.python.org/downloads/).
  - **QUAN TRỌNG**: Bạn phải tick chọn **"Add Python to PATH"** trước khi nhấn Install.
- **Kiểm tra**:
  ```bash
  python --version
  pip --version
  ```

#### 1.3. AWS CLI v2 (Quản lý dịch vụ AWS bằng dòng lệnh)
AWS CLI giúp bạn giao tiếp và tạo lập tài nguyên trên AWS thông qua các script tự động.
- **Cách cài đặt**:
  - Tải và cài đặt qua file MSI trên Windows: [AWS CLI V2 MSI](https://awscli.amazonaws.com/AWSCLIV2.msi).
- **Kiểm tra**:
  ```bash
  aws --version
  ```

#### 1.4. Git Bash (Khuyên dùng cho người dùng Windows)
Các script khởi tạo cơ sở dữ liệu và deploy trong dự án được viết dưới dạng Bash shell script (`.sh`). Do đó, nếu dùng Windows, bạn nên cài đặt Git Bash.
- **Cách cài đặt**: Tải [Git cho Windows](https://git-scm.com/download/win).
- Trong quá trình cài đặt, chọn tùy chọn **"Use Git and optional Unix tools from Windows Command Prompt"**.

---
### 2. Cấu hình thông tin xác thực AWS Credentials
Để AWS CLI có thể tạo tài nguyên trên tài khoản AWS của bạn, bạn cần cấp Access Key của một tài khoản IAM User có quyền Administrator hoặc các quyền cụ thể.

#### 2.1. Lấy Access Key từ AWS Console
1. Đăng nhập vào [AWS Management Console](https://console.aws.amazon.com/).
2. Tìm kiếm dịch vụ **IAM** trong thanh tìm kiếm.
3. Chọn **Users** ở menu bên trái → Click chọn tên User của bạn.
4. Chuyển sang tab **Security credentials**.
5. Kéo xuống phần **Access keys** và click **Create access key**.
6. Chọn loại **Command Line Interface (CLI)**, hoàn tất các bước và **tải file CSV chứa Access Key ID và Secret Access Key về máy**.

#### 2.2. Cấu hình AWS CLI trên máy tính của bạn
Mở Terminal (hoặc Git Bash) và chạy lệnh sau:
```bash
aws configure
```
Nhập các thông tin được yêu cầu tương ứng:
```text
AWS Access Key ID [None]: [Nhập Access Key ID của bạn]
AWS Secret Access Key [None]: [Nhập Secret Access Key của bạn]
Default region name [None]: us-east-1
Default output format [None]: json
```

> [!IMPORTANT]
> Dự án này sử dụng region mặc định là **us-east-1** (N. Virginia). Hãy đảm bảo bạn cấu hình đúng `us-east-1` để tất cả các dịch vụ được tạo trong cùng một khu vực, tránh lỗi kết nối chéo region.

#### 2.3. Xác minh cấu hình
Chạy lệnh sau để kiểm tra xem AWS CLI đã kết nối thành công tới tài khoản của bạn chưa:
```bash
aws sts get-caller-identity
```
Nếu hệ thống trả về JSON chứa `Account ID`, `Arn` và `UserId` của bạn, cấu hình đã hoàn tất và bạn có thể chuyển sang bước tiếp theo!
