---
title: "Preparation Steps"
date: 2026-07-10
weight: 2
chapter: false
---

### System Requirements

| Tool | Version | Purpose |
|------|---------|---------|
| Node.js | ≥ 18.x | Lambda runtime and Frontend build |
| npm | ≥ 9.x | JavaScript package manager |
| Python | ≥ 3.9 | Lambda functions and ML scripts |
| AWS CLI | v2 | Deploy and manage AWS resources |
| AWS SAM CLI | Latest | Deploy serverless applications |
| Git | Latest | Version control |

---

### 1. Install Tools

#### Node.js & npm
```bash
# Download: https://nodejs.org/
# Choose LTS version, Add to PATH

# Verify
node --version  # v18.x or higher
npm --version   # v9.x or higher
```

#### Python
```bash
# Download: https://www.python.org/downloads/
# Check: Add Python to PATH

# Install required packages
pip install boto3 pillow ultralytics geohash2

# Verify
python --version  # 3.9 or higher
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

### 2. Configure AWS Credentials

#### Step 1: Create IAM User with appropriate permissions
```
AWS Console → IAM → Users → Create user
→ Attach policies: AdministratorAccess (for workshop)
→ Security credentials → Create access key 
→ CLI → Create → Download CSV
```

#### Step 2: Configure AWS CLI
```bash
aws configure

# Enter information:
AWS Access Key ID: AKIA************
AWS Secret Access Key: **********************
Default region name: ap-southeast-1  # Or us-east-1
Default output format: json
```

#### Step 3: Verify
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

### 3. Clone TSL-SignMap Project

```bash
# Clone repository (example)
git clone https://github.com/your-org/tsl-signmap.git
cd tsl-signmap

# Directory structure
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

### 4. Required IAM Permissions

IAM account needs the following permissions:

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
- `AdministratorAccess` (recommended for workshop)
- Or: `PowerUserAccess` + `IAMFullAccess`

---

### 5. Setup Environment Variables

Create `.env` file in root directory:

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

### 7. Verify Environment

Run automated check script:

```bash
cd tsl-signmap
bash scripts/check-environment.sh
```

**Expected output:**
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

### 8. Create S3 Buckets

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

**Error: `aws: command not found`**
```bash
# Add AWS CLI to PATH
export PATH=$PATH:/usr/local/bin

# macOS/Linux: Add to ~/.bashrc or ~/.zshrc
echo 'export PATH=$PATH:/usr/local/bin' >> ~/.bashrc
```

**Error: `Unable to locate credentials`**
```bash
# Check credentials file
cat ~/.aws/credentials
cat ~/.aws/config

# Re-configure
aws configure
```

**Error: `Access Denied` during deployment**
```bash
# Check IAM permissions
aws iam get-user
aws iam list-attached-user-policies --user-name YOUR_USERNAME

# Test specific permission
aws dynamodb list-tables
aws s3 ls
```

**Error: `pip install` fails**
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

After completing preparation:
1. ✅ [Create DynamoDB Tables & Infrastructure](../5.3-infrastructure-database/)
2. ✅ [Deploy Backend & API Gateway](../5.4-backend-apigateway/)
3. ✅ [Deploy Frontend Application](../5.5-frontend-deployment/)
