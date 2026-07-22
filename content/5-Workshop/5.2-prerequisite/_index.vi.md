---
title: "Các bước chuẩn bị"
date: 2026-07-10
weight: 2
chapter: false
---

### Yêu cầu hệ thống

| Công cụ | Phiên bản | Mục đích |
|---------|-----------|----------|
| Node.js | ≥ 18.x | Runtime cho Lambda và build Frontend |
| npm | ≥ 9.x | Quản lý packages JavaScript |
| Python | ≥ 3.9 | Lambda functions và ML scripts |
| AWS CLI | v2 | Deploy và quản lý AWS resources |
| AWS SAM CLI | Latest | Deploy serverless applications |
| Git | Latest | Version control |

---

### 1. Cài đặt công cụ

#### Node.js & npm
```bash
# Download: https://nodejs.org/
# Chọn LTS version, Add to PATH

# Verify
node --version  # v18.x hoặc cao hơn
npm --version   # v9.x hoặc cao hơn
```

#### Python
```bash
# Download: https://www.python.org/downloads/
# Chọn: Add Python to PATH

# Cài đặt packages cần thiết
pip install boto3 pillow ultralytics geohash2

# Verify
python --version  # 3.9 hoặc cao hơn
pip --version
```

#### AWS CLI
```bash
# Windows: https://awscli.amazonaws.com/AWSCLIV2.msi
# macOS: brew install awscli
# Linux: https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html

# Verify
aws --version  # aws-cli/2.x
```

#### AWS SAM CLI
```bash
# Windows: https://github.com/aws/aws-sam-cli/releases/latest/download/AWS_SAM_CLI_64_PY3.msi
# macOS: brew install aws-sam-cli
# Linux: pip install aws-sam-cli

# Verify
sam --version  # SAM CLI, version 1.x
```

---

### 2. Cấu hình AWS Credentials

#### Bước 1: Tạo IAM User với quyền phù hợp
```
AWS Console → IAM → Users → Create user
→ Attach policies: AdministratorAccess (cho workshop)
→ Security credentials → Create access key 
→ CLI → Create → Download CSV
```

#### Bước 2: Configure AWS CLI
```bash
aws configure

# Nhập thông tin:
AWS Access Key ID: AKIA************
AWS Secret Access Key: **********************
Default region name: ap-southeast-1  # Hoặc us-east-1
Default output format: json
```

#### Bước 3: Verify
```bash
aws sts get-caller-identity

# Output:
{
  "UserId": "AIDA************",
  "Account": "123456789012",
  "Arn": "arn:aws:iam::123456789012:user/your-name"
}
```

---

### 3. Clone dự án TSL-SignMap

```bash
# Clone repository (giả định)
git clone https://github.com/your-org/tsl-signmap.git
cd tsl-signmap

# Cấu trúc thư mục
tsl-signmap/
├── backend/              # Lambda functions
│   ├── sign-submit/     # Submit sign API
│   ├── sign-vote/       # Voting API
│   ├── sign-query/      # Query signs by location
│   └── ai-detection/    # YOLO inference
├── frontend/            # React mobile web app
│   ├── src/
│   ├── public/
│   └── package.json
├── infrastructure/      # SAM/CloudFormation templates
│   ├── template.yaml
│   └── parameters.json
├── ml/                  # Machine learning
│   ├── yolo-model/
│   └── training/
└── scripts/            # Deployment scripts
    ├── deploy-all.sh
    └── cleanup.sh
```

---

### 4. Quyền IAM cần thiết

Tài khoản IAM cần các quyền sau:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "lambda:*",
        "apigateway:*",
        "dynamodb:*",
        "s3:*",
        "cognito-idp:*",
        "sagemaker:*",
        "sqs:*",
        "sns:*",
        "location:*",
        "iam:CreateRole",
        "iam:AttachRolePolicy",
        "iam:PassRole",
        "cloudformation:*",
        "cloudwatch:*",
        "logs:*"
      ],
      "Resource": "*"
    }
  ]
}
```

**Managed Policies:**
- `AdministratorAccess` (khuyến nghị cho workshop)
- Hoặc: `PowerUserAccess` + `IAMFullAccess`

---

### 5. Setup Environment Variables

Tạo file `.env` trong thư mục root:

```bash
# AWS Configuration
AWS_REGION=ap-southeast-1
AWS_ACCOUNT_ID=123456789012

# Project Configuration
PROJECT_NAME=tsl-signmap
ENVIRONMENT=dev

# DynamoDB Tables
SIGNS_TABLE=TSL-TrafficSigns-dev
USERS_TABLE=TSL-Users-dev
VOTES_TABLE=TSL-Votes-dev

# S3 Buckets
IMAGES_BUCKET=tsl-signmap-images-dev
FRONTEND_BUCKET=tsl-signmap-frontend-dev

# Cognito
USER_POOL_NAME=TSL-SignMap-Users
```

```bash
# Load environment variables
export $(cat .env | xargs)
```

---

### 6. Install Dependencies

#### Backend Dependencies
```bash
cd backend

# Install Node.js dependencies
cd sign-submit && npm install && cd ..
cd sign-vote && npm install && cd ..
cd sign-query && npm install && cd ..

# Install Python dependencies
cd ai-detection
pip install -r requirements.txt -t .
cd ..
```

#### Frontend Dependencies
```bash
cd frontend
npm install

# Verify packages
npm list react react-dom
```

---

### 7. Kiểm tra môi trường

Chạy script kiểm tra tự động:

```bash
cd tsl-signmap
bash scripts/check-environment.sh
```

**Output mong đợi:**
```
✓ Node.js: v18.17.0
✓ npm: v9.8.1
✓ Python: 3.9.7
✓ AWS CLI: 2.13.5
✓ SAM CLI: 1.95.0
✓ AWS Credentials: Configured
✓ AWS Region: ap-southeast-1
✓ IAM Permissions: Valid
✓ Dependencies: Installed

Environment check passed! Ready to deploy TSL-SignMap.
```

---

### 8. Tạo S3 Buckets

```bash
# Images bucket
aws s3 mb s3://tsl-signmap-images-dev \
  --region ap-southeast-1

# Frontend bucket
aws s3 mb s3://tsl-signmap-frontend-dev \
  --region ap-southeast-1

# Enable versioning for images
aws s3api put-bucket-versioning \
  --bucket tsl-signmap-images-dev \
  --versioning-configuration Status=Enabled

# Enable CORS for images bucket
aws s3api put-bucket-cors \
  --bucket tsl-signmap-images-dev \
  --cors-configuration file://config/cors.json
```

**cors.json:**
```json
{
  "CORSRules": [
    {
      "AllowedOrigins": ["*"],
      "AllowedMethods": ["GET", "PUT", "POST"],
      "AllowedHeaders": ["*"],
      "MaxAgeSeconds": 3000
    }
  ]
}
```

---

### Troubleshooting

**Lỗi: `aws: command not found`**
```bash
# Thêm AWS CLI vào PATH
export PATH=$PATH:/usr/local/bin

# macOS/Linux: Thêm vào ~/.bashrc hoặc ~/.zshrc
echo 'export PATH=$PATH:/usr/local/bin' >> ~/.bashrc
```

**Lỗi: `Unable to locate credentials`**
```bash
# Kiểm tra credentials file
cat ~/.aws/credentials
cat ~/.aws/config

# Re-configure
aws configure
```

**Lỗi: `Access Denied` khi deploy**
```bash
# Kiểm tra quyền IAM
aws iam get-user
aws iam list-attached-user-policies --user-name YOUR_USERNAME

# Test specific permission
aws dynamodb list-tables
aws s3 ls
```

**Lỗi: `pip install` fails**
```bash
# Upgrade pip
python -m pip install --upgrade pip

# Use virtual environment
python -m venv venv
source venv/bin/activate  # macOS/Linux
venv\Scripts\activate     # Windows
pip install -r requirements.txt
```

---

### Next Steps

Sau khi hoàn tất chuẩn bị:
1. ✅ [Tạo DynamoDB Tables & Infrastructure](../5.3-infrastructure-database/)
2. ✅ [Deploy Backend & API Gateway](../5.4-backend-apigateway/)
3. ✅ [Deploy Frontend Application](../5.5-frontend-deployment/)

