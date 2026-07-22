---
title: "Triển khai Backend & API Gateway"
date: 2026-07-10
weight: 4
chapter: false
---

### Tổng quan

Deploy 7 Lambda functions và REST API Gateway với Cognito Authorizer cho TSL-SignMap.

**Architecture:**
```
Mobile App → API Gateway → Cognito Authorizer → Lambda → DynamoDB/S3/SQS
```

---

### Lambda Functions

| Function | Method | Endpoint | Purpose |
|----------|--------|----------|---------|
| sign-submit | POST | /signs | Submit new traffic sign |
| sign-query | GET | /signs/nearby | Query signs by location |
| sign-vote | POST | /votes | Vote on sign submission |
| sign-approve | PUT | /signs/{id}/approve | Admin approve/reject |
| user-profile | GET | /users/me | Get user profile & coins |
| image-upload-url | GET | /signs/upload-url | Generate S3 presigned URL |
| ai-detection | SQS | - | Process image with YOLO |

---

### Bước 1: Tạo IAM Role

```bash
cat > lambda-trust-policy.json << 'EOF'
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Principal": { "Service": "lambda.amazonaws.com" },
    "Action": "sts:AssumeRole"
  }]
}
EOF

aws iam create-role \
  --role-name tsl-signmap-lambda-role \
  --assume-role-policy-document file://lambda-trust-policy.json

aws iam attach-role-policy \
  --role-name tsl-signmap-lambda-role \
  --policy-arn arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole

cat > lambda-permissions.json << 'EOF'
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Action": [
      "dynamodb:*",
      "s3:*",
      "sqs:*",
      "sns:*",
      "sagemaker:InvokeEndpoint",
      "geo:*"
    ],
    "Resource": "*"
  }]
}
EOF

aws iam put-role-policy \
  --role-name tsl-signmap-lambda-role \
  --policy-name TSLPermissions \
  --policy-document file://lambda-permissions.json
```

---

### Bước 2: Deploy Lambda Functions

```bash
cd backend

# Deploy với SAM
sam build
sam deploy \
  --stack-name tsl-signmap-backend \
  --parameter-overrides \
    SignsTable=tsl-signmap-TrafficSigns-dev \
    UsersTable=tsl-signmap-Users-dev \
    VotesTable=tsl-signmap-Votes-dev \
    ImagesBucket=tsl-signmap-images-$(aws sts get-caller-identity --query Account --output text) \
  --capabilities CAPABILITY_IAM
```

**template.yaml (snippet):**
```yaml
Resources:
  SignSubmitFunction:
    Type: AWS::Serverless::Function
    Properties:
      CodeUri: functions/sign-submit/
      Handler: index.handler
      Runtime: nodejs18.x
      Timeout: 30
      Environment:
        Variables:
          SIGNS_TABLE: !Ref SignsTable
          USERS_TABLE: !Ref UsersTable
          IMAGES_BUCKET: !Ref ImagesBucket
          QUEUE_URL: !GetAtt ImageProcessingQueue.QueueUrl
      Events:
        Api:
          Type: Api
          Properties:
            Path: /signs
            Method: POST
            Auth:
              Authorizer: CognitoAuthorizer
```

---

### Bước 3: Deploy API Gateway

```bash
API_ID=$(aws apigateway create-rest-api \
  --name tsl-signmap-api \
  --endpoint-configuration types=REGIONAL \
  --query 'id' \
  --output text)

AUTHORIZER_ID=$(aws apigateway create-authorizer \
  --rest-api-id $API_ID \
  --name CognitoAuth \
  --type COGNITO_USER_POOLS \
  --provider-arns arn:aws:cognito-idp:us-east-1:$(aws sts get-caller-identity --query Account --output text):userpool/$USER_POOL_ID \
  --identity-source method.request.header.Authorization \
  --query 'id' \
  --output text)

aws apigateway create-deployment \
  --rest-api-id $API_ID \
  --stage-name prod

echo "API: https://$API_ID.execute-api.us-east-1.amazonaws.com/prod"
```

---

### API Endpoints

| Method | Path | Auth | Description |
|--------|------|------|-------------|
| POST | /signs | Yes | Submit new sign with image |
| GET | /signs/nearby?lat=10.76&lng=106.66&radius=1 | Yes | Get signs within radius (km) |
| GET | /signs/{signId} | Yes | Get sign details |
| POST | /votes | Yes | Vote (upvote/downvote) on sign |
| GET | /users/me | Yes | Get profile, coins, reputation |
| GET | /signs/upload-url?filename=image.jpg | Yes | Get S3 presigned URL |
| PUT | /signs/{signId}/approve | Admin | Approve/reject sign |

---

### Testing

```bash
# Login to get token
TOKEN=$(aws cognito-idp initiate-auth \
  --client-id $CLIENT_ID \
  --auth-flow USER_PASSWORD_AUTH \
  --auth-parameters USERNAME=admin@tsl-signmap.com,PASSWORD=YourPassword \
  --query 'AuthenticationResult.IdToken' \
  --output text)

# Submit sign
curl -X POST https://$API_ID.execute-api.us-east-1.amazonaws.com/prod/signs \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "location": {"lat": 10.762622, "lng": 106.660172},
    "signType": "stop",
    "imageKey": "signs/user123/image.jpg"
  }'

# Query nearby signs
curl "https://$API_ID.execute-api.us-east-1.amazonaws.com/prod/signs/nearby?lat=10.76&lng=106.66&radius=2" \
  -H "Authorization: Bearer $TOKEN"

# Vote
curl -X POST https://$API_ID.execute-api.us-east-1.amazonaws.com/prod/votes \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"signId": "sign_12345", "voteType": "upvote"}'
```

---

### Monitoring

```bash
# CloudWatch Logs
aws logs tail /aws/lambda/sign-submit --follow

# API metrics
aws cloudwatch get-metric-statistics \
  --namespace AWS/ApiGateway \
  --metric-name Count \
  --dimensions Name=ApiName,Value=tsl-signmap-api \
  --start-time 2026-01-01T00:00:00Z \
  --end-time 2026-01-01T23:59:59Z \
  --period 3600 \
  --statistics Sum
```

---

### Cost Estimate

| Service | Usage | Cost/month |
|---------|-------|------------|
| Lambda | 1M invocations, 512MB, 2s | $10 |
| API Gateway | 1M requests | $3.50 |
| CloudWatch | 5GB logs | $2.50 |
| **Total** | | **$16/month** |

---

### Next: Frontend

```bash
cd ../5.5-frontend-deployment/
```
