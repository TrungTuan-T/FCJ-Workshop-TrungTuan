---
title: "Database & Infrastructure"
date: 2026-07-10
weight: 3
chapter: false
---

### Overview

This section deploys 5 core components:
- **DynamoDB**: 3 tables for data storage (Signs, Users, Votes)
- **S3**: 2 buckets (images and ML models)
- **SQS**: 1 queue for image processing
- **Cognito**: User Pool + Groups
- **Location Service**: Map index for geospatial queries

---

### Step 1: Setup AWS SAM

```bash
# Verify SAM CLI
sam --version

# Initialize project structure
cd tsl-signmap
sam init
```

**Project structure:**
```
tsl-signmap/
├── template.yaml          # SAM template
├── samconfig.toml         # SAM configuration
├── backend/               # Lambda functions
└── parameters.json        # Environment-specific params
```

---

### Step 2: Create DynamoDB Tables

#### Table Schema

| Table | Partition Key | Sort Key | GSI | Billing |
|-------|---------------|----------|-----|---------|
| TrafficSigns | SignID (S) | - | GeoHash-index, Status-index | On-Demand |
| Users | UserID (S) | - | Email-index | On-Demand |
| Votes | VoteID (S) | - | SignID-index, UserID-index | On-Demand |

#### Deploy Tables with SAM

**template.yaml (DynamoDB section):**
```yaml
Resources:
  TrafficSignsTable:
    Type: AWS::DynamoDB::Table
    Properties:
      TableName: !Sub ${ProjectName}-TrafficSigns-${Environment}
      BillingMode: PAY_PER_REQUEST
      AttributeDefinitions:
        - AttributeName: SignID
          AttributeType: S
        - AttributeName: GeoHash
          AttributeType: S
        - AttributeName: Status
          AttributeType: S
      KeySchema:
        - AttributeName: SignID
          KeyType: HASH
      GlobalSecondaryIndexes:
        - IndexName: GeoHash-index
          KeySchema:
            - AttributeName: GeoHash
              KeyType: HASH
          Projection:
            ProjectionType: ALL
        - IndexName: Status-index
          KeySchema:
            - AttributeName: Status
              KeyType: HASH
          Projection:
            ProjectionType: ALL
      StreamSpecification:
        StreamViewType: NEW_AND_OLD_IMAGES
      PointInTimeRecoverySpecification:
        PointInTimeRecoveryEnabled: true

  UsersTable:
    Type: AWS::DynamoDB::Table
    Properties:
      TableName: !Sub ${ProjectName}-Users-${Environment}
      BillingMode: PAY_PER_REQUEST
      AttributeDefinitions:
        - AttributeName: UserID
          AttributeType: S
        - AttributeName: Email
          AttributeType: S
      KeySchema:
        - AttributeName: UserID
          KeyType: HASH
      GlobalSecondaryIndexes:
        - IndexName: Email-index
          KeySchema:
            - AttributeName: Email
              KeyType: HASH
          Projection:
            ProjectionType: ALL

  VotesTable:
    Type: AWS::DynamoDB::Table
    Properties:
      TableName: !Sub ${ProjectName}-Votes-${Environment}
      BillingMode: PAY_PER_REQUEST
      AttributeDefinitions:
        - AttributeName: VoteID
          AttributeType: S
        - AttributeName: SignID
          AttributeType: S
        - AttributeName: UserID
          AttributeType: S
      KeySchema:
        - AttributeName: VoteID
          KeyType: HASH
      GlobalSecondaryIndexes:
        - IndexName: SignID-index
          KeySchema:
            - AttributeName: SignID
              KeyType: HASH
          Projection:
            ProjectionType: ALL
        - IndexName: UserID-index
          KeySchema:
            - AttributeName: UserID
              KeyType: HASH
          Projection:
            ProjectionType: ALL
      TimeToLiveSpecification:
        Enabled: true
        AttributeName: TTL
```

#### Deploy DynamoDB

```bash
# Deploy with SAM
sam build
sam deploy --guided

# Or use AWS CLI
bash scripts/create-dynamodb.sh us-east-1
```

**Script create-dynamodb.sh:**
```bash
#!/bin/bash
REGION=$1
PROJECT="tsl-signmap"
ENV="dev"

# TrafficSigns table
aws dynamodb create-table \
  --table-name ${PROJECT}-TrafficSigns-${ENV} \
  --attribute-definitions \
    AttributeName=SignID,AttributeType=S \
    AttributeName=GeoHash,AttributeType=S \
    AttributeName=Status,AttributeType=S \
  --key-schema AttributeName=SignID,KeyType=HASH \
  --global-secondary-indexes \
    IndexName=GeoHash-index,KeySchema=[{AttributeName=GeoHash,KeyType=HASH}],Projection={ProjectionType=ALL} \
    IndexName=Status-index,KeySchema=[{AttributeName=Status,KeyType=HASH}],Projection={ProjectionType=ALL} \
  --billing-mode PAY_PER_REQUEST \
  --stream-specification StreamEnabled=true,StreamViewType=NEW_AND_OLD_IMAGES \
  --region $REGION

echo "TrafficSigns table created"

# Users table
aws dynamodb create-table \
  --table-name ${PROJECT}-Users-${ENV} \
  --attribute-definitions \
    AttributeName=UserID,AttributeType=S \
    AttributeName=Email,AttributeType=S \
  --key-schema AttributeName=UserID,KeyType=HASH \
  --global-secondary-indexes \
    IndexName=Email-index,KeySchema=[{AttributeName=Email,KeyType=HASH}],Projection={ProjectionType=ALL} \
  --billing-mode PAY_PER_REQUEST \
  --region $REGION

echo "Users table created"

# Votes table
aws dynamodb create-table \
  --table-name ${PROJECT}-Votes-${ENV} \
  --attribute-definitions \
    AttributeName=VoteID,AttributeType=S \
    AttributeName=SignID,AttributeType=S \
    AttributeName=UserID,AttributeType=S \
  --key-schema AttributeName=VoteID,KeyType=HASH \
  --global-secondary-indexes \
    IndexName=SignID-index,KeySchema=[{AttributeName=SignID,KeyType=HASH}],Projection={ProjectionType=ALL} \
    IndexName=UserID-index,KeySchema=[{AttributeName=UserID,KeyType=HASH}],Projection={ProjectionType=ALL} \
  --billing-mode PAY_PER_REQUEST \
  --region $REGION

echo "Votes table created"
```

#### Verify Tables

```bash
aws dynamodb list-tables --region us-east-1

# Output:
{
  "TableNames": [
    "tsl-signmap-TrafficSigns-dev",
    "tsl-signmap-Users-dev",
    "tsl-signmap-Votes-dev"
  ]
}

# Check table details
aws dynamodb describe-table \
  --table-name tsl-signmap-TrafficSigns-dev \
  --query 'Table.[TableName,TableStatus,ItemCount]'
```

---

### Step 3: Create S3 Buckets

```bash
#!/bin/bash
REGION="us-east-1"
ACCOUNT_ID=$(aws sts get-caller-identity --query Account --output text)

# Images bucket
IMAGES_BUCKET="tsl-signmap-images-${ACCOUNT_ID}"
aws s3 mb s3://$IMAGES_BUCKET --region $REGION

# ML models bucket
MODELS_BUCKET="tsl-signmap-models-${ACCOUNT_ID}"
aws s3 mb s3://$MODELS_BUCKET --region $REGION

# Block public access for images
aws s3api put-public-access-block \
  --bucket $IMAGES_BUCKET \
  --public-access-block-configuration \
    BlockPublicAcls=true,\
    IgnorePublicAcls=true,\
    BlockPublicPolicy=false,\
    RestrictPublicBuckets=false

# Enable versioning for images
aws s3api put-bucket-versioning \
  --bucket $IMAGES_BUCKET \
  --versioning-configuration Status=Enabled

# Enable server-side encryption
aws s3api put-bucket-encryption \
  --bucket $IMAGES_BUCKET \
  --server-side-encryption-configuration '{
    "Rules": [{
      "ApplyServerSideEncryptionByDefault": {
        "SSEAlgorithm": "AES256"
      }
    }]
  }'

echo "Images bucket: s3://$IMAGES_BUCKET"
echo "Models bucket: s3://$MODELS_BUCKET"
```

#### Configure CORS for Images Bucket

```bash
cat > cors.json << 'EOF'
{
  "CORSRules": [{
    "AllowedHeaders": ["*"],
    "AllowedMethods": ["GET", "PUT", "POST", "HEAD"],
    "AllowedOrigins": ["*"],
    "ExposeHeaders": ["ETag", "x-amz-request-id"],
    "MaxAgeSeconds": 3000
  }]
}
EOF

aws s3api put-bucket-cors \
  --bucket $IMAGES_BUCKET \
  --cors-configuration file://cors.json
```

#### Setup Lifecycle Policy

```bash
cat > lifecycle.json << 'EOF'
{
  "Rules": [{
    "Id": "DeleteOldVersions",
    "Status": "Enabled",
    "NoncurrentVersionExpiration": {
      "NoncurrentDays": 30
    }
  }]
}
EOF

aws s3api put-bucket-lifecycle-configuration \
  --bucket $IMAGES_BUCKET \
  --lifecycle-configuration file://lifecycle.json
```

---

### Step 4: Create SQS Queue

```bash
# Queue for image processing
QUEUE_URL=$(aws sqs create-queue \
  --queue-name tsl-signmap-image-processing \
  --attributes '{
    "VisibilityTimeout": "900",
    "MessageRetentionPeriod": "345600",
    "ReceiveMessageWaitTimeSeconds": "20"
  }' \
  --region us-east-1 \
  --query 'QueueUrl' \
  --output text)

# Dead Letter Queue
DLQ_URL=$(aws sqs create-queue \
  --queue-name tsl-signmap-image-processing-dlq \
  --region us-east-1 \
  --query 'QueueUrl' \
  --output text)

# Get DLQ ARN
DLQ_ARN=$(aws sqs get-queue-attributes \
  --queue-url $DLQ_URL \
  --attribute-names QueueArn \
  --query 'Attributes.QueueArn' \
  --output text)

# Configure redrive policy
aws sqs set-queue-attributes \
  --queue-url $QUEUE_URL \
  --attributes "{
    \"RedrivePolicy\": \"{\\\"deadLetterTargetArn\\\":\\\"$DLQ_ARN\\\",\\\"maxReceiveCount\\\":\\\"3\\\"}\"
  }"

echo "Queue URL: $QUEUE_URL"
echo "DLQ ARN: $DLQ_ARN"
```

**Queue Configuration:**
- Visibility Timeout: 15 minutes (for AI inference)
- Message Retention: 4 days
- Max Receive Count: 3 times → DLQ
- Long Polling: 20 seconds

---

### Step 5: Setup Cognito User Pool

```bash
#!/bin/bash

# Create User Pool
USER_POOL_ID=$(aws cognito-idp create-user-pool \
  --pool-name tsl-signmap-users \
  --policies '{
    "PasswordPolicy": {
      "MinimumLength": 8,
      "RequireUppercase": true,
      "RequireLowercase": true,
      "RequireNumbers": true,
      "RequireSymbols": false
    }
  }' \
  --auto-verified-attributes email \
  --mfa-configuration OPTIONAL \
  --account-recovery-setting '{
    "RecoveryMechanisms": [{
      "Priority": 1,
      "Name": "verified_email"
    }]
  }' \
  --user-attribute-update-settings '{
    "AttributesRequireVerificationBeforeUpdate": ["email"]
  }' \
  --query 'UserPool.Id' \
  --output text)

echo "User Pool ID: $USER_POOL_ID"

# Create App Client
CLIENT_ID=$(aws cognito-idp create-user-pool-client \
  --user-pool-id $USER_POOL_ID \
  --client-name tsl-signmap-mobile-client \
  --no-generate-secret \
  --explicit-auth-flows \
    ALLOW_USER_PASSWORD_AUTH \
    ALLOW_USER_SRP_AUTH \
    ALLOW_REFRESH_TOKEN_AUTH \
  --access-token-validity 60 \
  --id-token-validity 60 \
  --refresh-token-validity 30 \
  --token-validity-units '{
    "AccessToken": "minutes",
    "IdToken": "minutes",
    "RefreshToken": "days"
  }' \
  --query 'UserPoolClient.ClientId' \
  --output text)

echo "Client ID: $CLIENT_ID"

# Create User Groups
for group in Admin Contributor Driver; do
  aws cognito-idp create-group \
    --user-pool-id $USER_POOL_ID \
    --group-name $group \
    --description "$group role for TSL-SignMap"
done

# Create test admin user
aws cognito-idp admin-create-user \
  --user-pool-id $USER_POOL_ID \
  --username admin@tsl-signmap.com \
  --user-attributes \
    Name=email,Value=admin@tsl-signmap.com \
    Name=email_verified,Value=true \
  --temporary-password "TempPass123" \
  --message-action SUPPRESS

# Add to Admin group
aws cognito-idp admin-add-user-to-group \
  --user-pool-id $USER_POOL_ID \
  --username admin@tsl-signmap.com \
  --group-name Admin

echo "Admin user created: admin@tsl-signmap.com / TempPass123"
```

**Save credentials:**
```
UserPoolId: ap-southeast-1_XXXXXXXXX
ClientId: 7n8g9hcus0ehbmk91bcreuplbv
Admin: admin@tsl-signmap.com
Password: TempPass123 (temporary)
```

---

### Step 6: Setup Amazon Location Service

```bash
# Create Place Index for geocoding
aws location create-place-index \
  --index-name TSL-SignMap-PlaceIndex \
  --data-source Esri \
  --pricing-plan RequestBasedUsage \
  --region us-east-1

# Create Route Calculator
aws location create-route-calculator \
  --calculator-name TSL-RouteCalculator \
  --data-source Esri \
  --pricing-plan RequestBasedUsage \
  --region us-east-1

echo "Location Service configured"
```

---

### Environment Variables

Create `.env` file for backend:

```bash
cat > backend/.env << EOF
# AWS Configuration
AWS_REGION=us-east-1
AWS_ACCOUNT_ID=$(aws sts get-caller-identity --query Account --output text)

# DynamoDB Tables
SIGNS_TABLE=tsl-signmap-TrafficSigns-dev
USERS_TABLE=tsl-signmap-Users-dev
VOTES_TABLE=tsl-signmap-Votes-dev

# S3 Buckets
IMAGES_BUCKET=tsl-signmap-images-$(aws sts get-caller-identity --query Account --output text)
MODELS_BUCKET=tsl-signmap-models-$(aws sts get-caller-identity --query Account --output text)

# SQS
IMAGE_PROCESSING_QUEUE_URL=$QUEUE_URL

# Cognito
USER_POOL_ID=$USER_POOL_ID
CLIENT_ID=$CLIENT_ID

# Location Service
PLACE_INDEX_NAME=TSL-SignMap-PlaceIndex
ROUTE_CALCULATOR_NAME=TSL-RouteCalculator

# Application
COINS_PER_SUBMISSION=10
COINS_PER_VOTE=1
DAILY_VOTE_LIMIT=5
AUTO_APPROVE_THRESHOLD=0.7
AUTO_REJECT_THRESHOLD=0.3
EOF

echo ".env file created"
```

---

### Verification Checklist

```bash
#!/bin/bash
# Script: verify-infrastructure.sh

echo "✓ Checking DynamoDB tables..."
aws dynamodb list-tables --region us-east-1 | grep tsl-signmap

echo "✓ Checking S3 buckets..."
aws s3 ls | grep tsl-signmap

echo "✓ Checking SQS queues..."
aws sqs list-queues --region us-east-1 | grep tsl-signmap

echo "✓ Checking Cognito User Pool..."
aws cognito-idp list-user-pools --max-results 10 --region us-east-1 | grep tsl-signmap

echo "✓ Checking Location Service..."
aws location list-place-indexes --region us-east-1

echo "Infrastructure verification complete!"
```

**Expected Output:**
```
✓ 3/3 DynamoDB tables created
✓ 2/2 S3 buckets configured
✓ SQS queue with DLQ ready
✓ Cognito User Pool with 3 groups
✓ Location Service indexes created
```

---

### Cost Estimation (Monthly)

| Service | Configuration | Estimated Cost |
|---------|---------------|----------------|
| DynamoDB | On-Demand, 3 tables, 1M reads, 100K writes | $1.50 |
| S3 | 100K images (50GB) | $1.15 |
| SQS | 100K messages | $0.04 |
| Cognito | 5K MAU | $0 (free tier) |
| Location Service | 100K geocoding requests | $5 |
| **TOTAL** | | **~$7.69/month** |

**Note:** Actual costs depend on usage. On-Demand billing scales with traffic.

---

### Next: Deploy Backend API

```bash
# Move to next step
cd ../5.4-backend-apigateway/
```

