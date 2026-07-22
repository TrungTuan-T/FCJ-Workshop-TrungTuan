---
title: "Testing & Cleanup"
date: 2026-07-10
weight: 6
chapter: false
---

### Overview

End-to-end testing of TSL-SignMap system and cleanup of all AWS resources after completing the workshop.

---

### Step 1: E2E Testing

#### Test Authentication

```bash
# Register new user
curl -X POST \
  https://cognito-idp.us-east-1.amazonaws.com/ \
  -H 'Content-Type: application/x-amz-json-1.1' \
  -H 'X-Amz-Target: AWSCognitoIdentityProviderService.SignUp' \
  -d '{
    "ClientId": "'$CLIENT_ID'",
    "Username": "testuser@example.com",
    "Password": "TestPass123!",
    "UserAttributes": [
      {"Name": "email", "Value": "testuser@example.com"}
    ]
  }'

# Login
TOKEN=$(aws cognito-idp initiate-auth \
  --client-id $CLIENT_ID \
  --auth-flow USER_PASSWORD_AUTH \
  --auth-parameters USERNAME=testuser@example.com,PASSWORD=TestPass123! \
  --query 'AuthenticationResult.IdToken' \
  --output text)

echo "Token: $TOKEN"
```

#### Test Sign Submission

```bash
# 1. Get upload URL
UPLOAD_RESPONSE=$(curl -X GET \
  "https://$API_ID.execute-api.us-east-1.amazonaws.com/prod/signs/upload-url?filename=test-sign.jpg" \
  -H "Authorization: Bearer $TOKEN")

UPLOAD_URL=$(echo $UPLOAD_RESPONSE | jq -r '.uploadUrl')
IMAGE_KEY=$(echo $UPLOAD_RESPONSE | jq -r '.imageKey')

# 2. Upload image to S3
curl -X PUT "$UPLOAD_URL" \
  -H "Content-Type: image/jpeg" \
  --data-binary "@test-images/stop-sign.jpg"

# 3. Submit sign metadata
curl -X POST \
  https://$API_ID.execute-api.us-east-1.amazonaws.com/prod/signs \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "location": {
      "lat": 10.762622,
      "lng": 106.660172
    },
    "signType": "stop",
    "imageKey": "'$IMAGE_KEY'"
  }'
```

#### Test Query Nearby Signs

```bash
curl -X GET \
  "https://$API_ID.execute-api.us-east-1.amazonaws.com/prod/signs/nearby?lat=10.76&lng=106.66&radius=5" \
  -H "Authorization: Bearer $TOKEN"
```

#### Test Voting

```bash
# Get a sign ID from previous query
SIGN_ID="sign_12345"

# Vote
curl -X POST \
  https://$API_ID.execute-api.us-east-1.amazonaws.com/prod/votes \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "signId": "'$SIGN_ID'",
    "voteType": "upvote"
  }'

# Check updated vote stats
curl -X GET \
  "https://$API_ID.execute-api.us-east-1.amazonaws.com/prod/signs/$SIGN_ID" \
  -H "Authorization: Bearer $TOKEN"
```

#### Test User Profile

```bash
# Get profile with coins and reputation
curl -X GET \
  https://$API_ID.execute-api.us-east-1.amazonaws.com/prod/users/me \
  -H "Authorization: Bearer $TOKEN"

# Expected response:
{
  "userId": "user_abc123",
  "email": "testuser@example.com",
  "coinBalance": 21,
  "reputationScore": 52,
  "totalSubmissions": 1,
  "totalVotes": 1
}
```

---

### Step 2: Load Testing

```bash
# Install artillery
npm install -g artillery

# Create load test scenario
cat > loadtest.yml << 'EOF'
config:
  target: "https://$API_ID.execute-api.us-east-1.amazonaws.com/prod"
  phases:
    - duration: 60
      arrivalRate: 10
      name: "Warm up"
    - duration: 120
      arrivalRate: 50
      name: "Sustained load"
  defaults:
    headers:
      Authorization: "Bearer $TOKEN"

scenarios:
  - name: "Query nearby signs"
    flow:
      - get:
          url: "/signs/nearby?lat=10.76&lng=106.66&radius=2"
  
  - name: "Get user profile"
    flow:
      - get:
          url: "/users/me"
EOF

# Run load test
artillery run loadtest.yml
```

**Expected results:**
- 95% requests < 500ms
- 0% errors
- No Lambda throttling

---

### Step 3: Verify All Components

```bash
#!/bin/bash
# Script: verify-system.sh

echo "=== TSL-SignMap System Verification ==="

# DynamoDB
echo "✓ Checking DynamoDB tables..."
aws dynamodb list-tables | grep tsl-signmap | wc -l
# Expected: 3

# S3
echo "✓ Checking S3 buckets..."
aws s3 ls | grep tsl-signmap | wc -l
# Expected: 2

# Lambda
echo "✓ Checking Lambda functions..."
aws lambda list-functions | grep tsl-signmap | wc -l
# Expected: 7

# API Gateway
echo "✓ Checking API Gateway..."
aws apigateway get-rest-apis | grep tsl-signmap

# Cognito
echo "✓ Checking Cognito User Pool..."
aws cognito-idp list-user-pools --max-results 10 | grep tsl-signmap

# CloudFront
echo "✓ Checking CloudFront distribution..."
aws cloudfront list-distributions | grep tsl-signmap

# CloudWatch Logs
echo "✓ Checking CloudWatch Logs..."
aws logs describe-log-groups | grep /aws/lambda | grep tsl | wc -l

echo "=== Verification Complete ==="
```

---

### Step 4: Cleanup Resources

⚠️ **WARNING**: This script will DELETE ALL resources. Only run after completing the workshop!

```bash
#!/bin/bash
# Script: cleanup-all.sh

set -e

echo "🗑️  Starting cleanup of TSL-SignMap resources..."

# Get Account ID
ACCOUNT_ID=$(aws sts get-caller-identity --query Account --output text)

# 1. Delete CloudFront Distribution
echo "Deleting CloudFront..."
DIST_ID=$(aws cloudfront list-distributions \
  --query "DistributionList.Items[?Comment=='TSL-SignMap Frontend'].Id" \
  --output text)

if [ ! -z "$DIST_ID" ]; then
  # Disable distribution
  aws cloudfront get-distribution-config --id $DIST_ID > dist-config.json
  ETAG=$(aws cloudfront get-distribution-config --id $DIST_ID --query 'ETag' --output text)
  
  jq '.DistributionConfig.Enabled = false' dist-config.json > dist-config-disabled.json
  
  aws cloudfront update-distribution \
    --id $DIST_ID \
    --distribution-config file://dist-config-disabled.json \
    --if-match $ETAG
  
  echo "Waiting for distribution to disable (5 min)..."
  sleep 300
  
  # Delete distribution
  ETAG=$(aws cloudfront get-distribution-config --id $DIST_ID --query 'ETag' --output text)
  aws cloudfront delete-distribution --id $DIST_ID --if-match $ETAG
  
  rm dist-config.json dist-config-disabled.json
fi

# 2. Delete S3 Buckets
echo "Deleting S3 buckets..."
for bucket in $(aws s3 ls | grep tsl-signmap | awk '{print $3}'); do
  echo "  Emptying bucket: $bucket"
  aws s3 rm s3://$bucket --recursive
  aws s3 rb s3://$bucket
done

# 3. Delete Lambda Functions
echo "Deleting Lambda functions..."
for func in $(aws lambda list-functions --query 'Functions[?contains(FunctionName, `tsl-signmap`)].FunctionName' --output text); do
  aws lambda delete-function --function-name $func
done

# 4. Delete API Gateway
echo "Deleting API Gateway..."
API_ID=$(aws apigateway get-rest-apis \
  --query 'items[?name==`tsl-signmap-api`].id' \
  --output text)

if [ ! -z "$API_ID" ]; then
  aws apigateway delete-rest-api --rest-api-id $API_ID
fi

# 5. Delete SQS Queues
echo "Deleting SQS queues..."
for queue in $(aws sqs list-queues --query 'QueueUrls[?contains(@, `tsl-signmap`)]' --output text); do
  aws sqs delete-queue --queue-url $queue
done

# 6. Delete DynamoDB Tables
echo "Deleting DynamoDB tables..."
for table in tsl-signmap-TrafficSigns-dev tsl-signmap-Users-dev tsl-signmap-Votes-dev; do
  aws dynamodb delete-table --table-name $table 2>/dev/null || true
done

# 7. Delete Cognito User Pool
echo "Deleting Cognito User Pool..."
USER_POOL_ID=$(aws cognito-idp list-user-pools --max-results 10 \
  --query 'UserPools[?Name==`tsl-signmap-users`].Id' \
  --output text)

if [ ! -z "$USER_POOL_ID" ]; then
  aws cognito-idp delete-user-pool --user-pool-id $USER_POOL_ID
fi

# 8. Delete Location Service Resources
echo "Deleting Location Service..."
aws location delete-place-index --index-name TSL-SignMap-PlaceIndex 2>/dev/null || true
aws location delete-route-calculator --calculator-name TSL-RouteCalculator 2>/dev/null || true

# 9. Delete CloudWatch Log Groups
echo "Deleting CloudWatch Logs..."
for log_group in $(aws logs describe-log-groups --query 'logGroups[?contains(logGroupName, `tsl-signmap`)].logGroupName' --output text); do
  aws logs delete-log-group --log-group-name $log_group
done

# 10. Delete IAM Role
echo "Deleting IAM Role..."
ROLE_NAME="tsl-signmap-lambda-role"
aws iam delete-role-policy --role-name $ROLE_NAME --policy-name TSLPermissions 2>/dev/null || true
aws iam detach-role-policy --role-name $ROLE_NAME --policy-arn arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole 2>/dev/null || true
aws iam delete-role --role-name $ROLE_NAME 2>/dev/null || true

# 11. Delete CloudFormation Stack (if using SAM)
echo "Deleting CloudFormation stack..."
aws cloudformation delete-stack --stack-name tsl-signmap-backend 2>/dev/null || true

echo "✅ Cleanup complete!"
echo "Note: Some resources may take a few minutes to fully delete."
```

**Run cleanup:**
```bash
bash scripts/cleanup-all.sh
```

---

### Verification After Cleanup

```bash
# Verify no resources remaining
aws dynamodb list-tables | grep tsl-signmap
aws s3 ls | grep tsl-signmap
aws lambda list-functions | grep tsl-signmap
aws apigateway get-rest-apis | grep tsl-signmap

# Should return empty results
```

---

### Cost Summary

| Phase | Duration | Estimated Cost |
|-------|----------|----------------|
| **Development** | 1 week | ~$20 |
| **Testing** | 2 days | ~$10 |
| **Production** (1 month, 5K users) | 30 days | ~$80 |
| **TOTAL Workshop** | | **~$30** |

**Free Tier Coverage:**
- Cognito: 50K MAU/month
- Lambda: 1M requests/month
- DynamoDB: 25GB storage
- S3: 5GB storage

---

### Conclusion

You've completed the TSL-SignMap workshop! 🎉

**What You've Learned:**
- ✅ Serverless architecture with Lambda, DynamoDB, API Gateway
- ✅ AI integration with SageMaker (YOLO)
- ✅ Geospatial queries with Location Service
- ✅ Authentication with Cognito
- ✅ Frontend deployment with S3 + CloudFront
- ✅ Voting system and reputation management
- ✅ Cost optimization strategies

**Next Steps:**
- Deploy SageMaker endpoint with YOLO model
- Implement real-time notifications with SNS
- Add analytics dashboard
- Setup monitoring alerts
- Implement CI/CD pipeline
