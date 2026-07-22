---
title: "Verify Infrastructure"
date: 2026
weight: 2
chapter: false
pre: " <b> 5.3.2 </b> "
---

### Verify Created Resources

After deploying infrastructure, verify that all resources have been created successfully.

---

### 1. Check DynamoDB Tables

```bash
# List all tables
aws dynamodb list-tables

# Describe TrafficSigns table
aws dynamodb describe-table \
  --table-name TSL-TrafficSigns-dev \
  --query 'Table.[TableName,TableStatus,ItemCount,GlobalSecondaryIndexes[*].IndexName]' \
  --output table

# Describe Users table
aws dynamodb describe-table \
  --table-name TSL-Users-dev \
  --query 'Table.[TableName,TableStatus,ItemCount]' \
  --output table

# Describe Votes table
aws dynamodb describe-table \
  --table-name TSL-Votes-dev \
  --query 'Table.[TableName,TableStatus,GlobalSecondaryIndexes[*].IndexName]' \
  --output table
```

**Expected output:**
```
TrafficSigns: ACTIVE, GSI: location-index, user-index
Users: ACTIVE
Votes: ACTIVE, GSI: sign-index, user-index
```

---

### 2. Check S3 Buckets

```bash
# List buckets
aws s3 ls | grep tsl-signmap

# Check images bucket configuration
aws s3api get-bucket-versioning \
  --bucket tsl-signmap-images-dev

aws s3api get-bucket-cors \
  --bucket tsl-signmap-images-dev

# Check bucket encryption
aws s3api get-bucket-encryption \
  --bucket tsl-signmap-images-dev
```

**Test image upload:**
```bash
# Create test image
echo "test image data" > test-sign.jpg

# Upload to S3
aws s3 cp test-sign.jpg \
  s3://tsl-signmap-images-dev/test/test-sign.jpg

# Verify upload
aws s3 ls s3://tsl-signmap-images-dev/test/

# Clean up test
aws s3 rm s3://tsl-signmap-images-dev/test/test-sign.jpg
```

---

### 3. Check Cognito User Pool

```bash
# Get User Pool details
aws cognito-idp list-user-pools --max-results 10 \
  | grep -A 5 "TSL-SignMap"

# Describe User Pool (replace POOL_ID)
POOL_ID=$(aws cognito-idp list-user-pools --max-results 10 \
  --query "UserPools[?Name=='TSL-SignMap-Users'].Id" \
  --output text)

aws cognito-idp describe-user-pool \
  --user-pool-id $POOL_ID \
  --query 'UserPool.[Id,Name,Status,MfaConfiguration]' \
  --output table

# Check User Pool Client
aws cognito-idp list-user-pool-clients \
  --user-pool-id $POOL_ID
```

---

### 4. Check SQS Queue

```bash
# List queues
aws sqs list-queues | grep tsl-signmap

# Get queue URL
QUEUE_URL=$(aws sqs list-queues \
  --query "QueueUrls[?contains(@, 'tsl-signmap-ai-queue')]" \
  --output text)

# Get queue attributes
aws sqs get-queue-attributes \
  --queue-url $QUEUE_URL \
  --attribute-names All \
  --query 'Attributes.[ApproximateNumberOfMessages,VisibilityTimeout,MessageRetentionPeriod]' \
  --output table
```

---

### 5. Check Location Service

```bash
# List place indexes
aws location list-place-indexes

# Describe place index
aws location describe-place-index \
  --index-name TSL-SignMap-PlaceIndex \
  --query '[IndexName,DataSource,Description]' \
  --output table

# Test geocoding (find coordinates from address)
aws location search-place-index-for-text \
  --index-name TSL-SignMap-PlaceIndex \
  --text "Ho Chi Minh City, Vietnam" \
  --max-results 1 \
  --query 'Results[0].[Place.Label,Place.Geometry.Point]' \
  --output table
```

---

### 6. Test Sample Data Write to DynamoDB

#### Create test user
```bash
aws dynamodb put-item \
  --table-name TSL-Users-dev \
  --item '{
    "userId": {"S": "test-user-001"},
    "username": {"S": "testuser"},
    "email": {"S": "test@example.com"},
    "reputation": {"N": "100"},
    "totalSubmissions": {"N": "0"},
    "totalVotes": {"N": "0"},
    "createdAt": {"S": "2026-07-22T10:00:00Z"}
  }'

# Verify
aws dynamodb get-item \
  --table-name TSL-Users-dev \
  --key '{"userId": {"S": "test-user-001"}}' \
  --query 'Item.[username.S,email.S,reputation.N]' \
  --output table
```

#### Create test traffic sign
```bash
aws dynamodb put-item \
  --table-name TSL-TrafficSigns-dev \
  --item '{
    "signId": {"S": "sign-001"},
    "userId": {"S": "test-user-001"},
    "latitude": {"N": "10.7769"},
    "longitude": {"N": "106.7009"},
    "signType": {"S": "STOP"},
    "confidence": {"N": "0.95"},
    "status": {"S": "pending"},
    "voteCount": {"N": "0"},
    "imageUrl": {"S": "s3://tsl-signmap-images-dev/test/sign-001.jpg"},
    "createdAt": {"S": "2026-07-22T10:05:00Z"},
    "geohash": {"S": "w679"}
  }'

# Verify
aws dynamodb get-item \
  --table-name TSL-TrafficSigns-dev \
  --key '{"signId": {"S": "sign-001"}}' \
  --query 'Item.[signType.S,latitude.N,longitude.N,status.S]' \
  --output table
```

---

### 7. Test Location-based Query (GSI)

```bash
# Query signs by geohash (location-based search)
aws dynamodb query \
  --table-name TSL-TrafficSigns-dev \
  --index-name location-index \
  --key-condition-expression "geohash = :gh" \
  --expression-attribute-values '{":gh":{"S":"w679"}}' \
  --query 'Items[*].[signId.S,signType.S,latitude.N,longitude.N]' \
  --output table

# Query signs by userId
aws dynamodb query \
  --table-name TSL-TrafficSigns-dev \
  --index-name user-index \
  --key-condition-expression "userId = :uid" \
  --expression-attribute-values '{":uid":{"S":"test-user-001"}}' \
  --query 'Items[*].[signId.S,signType.S,createdAt.S]' \
  --output table
```

---

### 8. Test SQS Message Flow

```bash
# Send test message to AI processing queue
QUEUE_URL=$(aws sqs list-queues \
  --query "QueueUrls[?contains(@, 'tsl-signmap-ai-queue')]" \
  --output text)

aws sqs send-message \
  --queue-url $QUEUE_URL \
  --message-body '{
    "signId": "sign-001",
    "imageUrl": "s3://tsl-signmap-images-dev/test/sign-001.jpg",
    "userId": "test-user-001"
  }'

# Receive message (verify delivery)
aws sqs receive-message \
  --queue-url $QUEUE_URL \
  --max-number-of-messages 1 \
  --query 'Messages[0].[MessageId,Body]' \
  --output table

# Delete message (cleanup)
RECEIPT_HANDLE=$(aws sqs receive-message \
  --queue-url $QUEUE_URL \
  --max-number-of-messages 1 \
  --query 'Messages[0].ReceiptHandle' \
  --output text)

aws sqs delete-message \
  --queue-url $QUEUE_URL \
  --receipt-handle "$RECEIPT_HANDLE"
```

---

### 9. Cleanup Test Data

```bash
# Delete test traffic sign
aws dynamodb delete-item \
  --table-name TSL-TrafficSigns-dev \
  --key '{"signId": {"S": "sign-001"}}'

# Delete test user
aws dynamodb delete-item \
  --table-name TSL-Users-dev \
  --key '{"userId": {"S": "test-user-001"}}'

# Verify deletion
aws dynamodb scan \
  --table-name TSL-Users-dev \
  --filter-expression "userId = :uid" \
  --expression-attribute-values '{":uid":{"S":"test-user-001"}}' \
  --select COUNT
```

---

### 10. Check IAM Roles

```bash
# List Lambda execution roles
aws iam list-roles \
  | grep -i "tsl-signmap\|lambda"

# Check specific role (example)
aws iam get-role \
  --role-name TSL-SignMap-Lambda-Role \
  --query 'Role.[RoleName,Arn]' \
  --output table

# List attached policies
aws iam list-attached-role-policies \
  --role-name TSL-SignMap-Lambda-Role
```

---

### Verification Checklist

| Resource | Status | Command |
|----------|--------|---------|
| DynamoDB Tables (3) | ✓ | `aws dynamodb list-tables` |
| S3 Buckets (2) | ✓ | `aws s3 ls \| grep tsl` |
| Cognito User Pool | ✓ | `aws cognito-idp list-user-pools --max-results 10` |
| SQS Queue | ✓ | `aws sqs list-queues` |
| Location Place Index | ✓ | `aws location list-place-indexes` |
| IAM Roles | ✓ | `aws iam list-roles \| grep tsl` |

---

### Troubleshooting

**Error: Table not found**
```bash
# Check if CloudFormation stack deployed successfully
aws cloudformation describe-stacks \
  --stack-name tsl-signmap-infrastructure \
  --query 'Stacks[0].StackStatus'

# Check stack events for errors
aws cloudformation describe-stack-events \
  --stack-name tsl-signmap-infrastructure \
  --max-items 10
```

**Error: Access Denied**
```bash
# Verify your IAM permissions
aws sts get-caller-identity

# Test specific service access
aws dynamodb list-tables
aws s3 ls
aws sqs list-queues
```

**Error: Invalid region**
```bash
# Check configured region
aws configure get region

# Set correct region
export AWS_DEFAULT_REGION=ap-southeast-1
```

---

### Next Steps

Infrastructure is working! Next:
1. ✅ [Deploy Backend Lambda Functions](../../5.4-backend-apigateway/)
2. ✅ [Setup API Gateway](../../5.4-backend-apigateway/)
3. ✅ [Deploy Frontend Application](../../5.5-frontend-deployment/)
