---
title: "Prepare Lambda Functions"
date: 2026-07-10
weight: 1
chapter: false
---

### Overview

Prepare code and dependencies for TSL-SignMap Lambda functions.

---

### Step 1: Create Project Structure

```bash
mkdir -p backend/functions/{sign-submit,sign-query,sign-vote,sign-approve,user-profile,upload-url,ai-detection}

# Structure
backend/
├── functions/
│   ├── sign-submit/
│   │   ├── index.js
│   │   └── package.json
│   ├── sign-query/
│   ├── sign-vote/
│   ├── sign-approve/
│   ├── user-profile/
│   ├── upload-url/
│   └── ai-detection/
└── shared/
    ├── dynamodb.js
    └── utils.js
```

---

### Step 2: Install Dependencies

#### sign-submit Function

```bash
cd backend/functions/sign-submit

cat > package.json << 'EOF'
{
  "name": "sign-submit",
  "version": "1.0.0",
  "dependencies": {
    "aws-sdk": "^2.1400.0",
    "uuid": "^9.0.0",
    "geohash": "^0.2.0"
  }
}
EOF

npm install
```

#### sign-query Function

```bash
cd ../sign-query

cat > package.json << 'EOF'
{
  "name": "sign-query",
  "version": "1.0.0",
  "dependencies": {
    "aws-sdk": "^2.1400.0",
    "geohash": "^0.2.0"
  }
}
EOF

npm install
```

#### sign-vote Function

```bash
cd ../sign-vote

cat > package.json << 'EOF'
{
  "name": "sign-vote",
  "version": "1.0.0",
  "dependencies": {
    "aws-sdk": "^2.1400.0",
    "uuid": "^9.0.0"
  }
}
EOF

npm install
```

---

### Step 3: Shared Utilities

```bash
cd ../../shared

cat > dynamodb.js << 'EOF'
const AWS = require('aws-sdk');
const dynamodb = new AWS.DynamoDB.DocumentClient();

async function getItem(tableName, key) {
  const params = {
    TableName: tableName,
    Key: key
  };
  const result = await dynamodb.get(params).promise();
  return result.Item;
}

async function putItem(tableName, item) {
  const params = {
    TableName: tableName,
    Item: item
  };
  await dynamodb.put(params).promise();
  return item;
}

async function queryByGeoHash(tableName, geoHash) {
  const params = {
    TableName: tableName,
    IndexName: 'GeoHash-index',
    KeyConditionExpression: 'GeoHash = :gh',
    ExpressionAttributeValues: {
      ':gh': geoHash
    }
  };
  const result = await dynamodb.query(params).promise();
  return result.Items;
}

module.exports = { getItem, putItem, queryByGeoHash };
EOF

cat > utils.js << 'EOF'
function response(statusCode, body) {
  return {
    statusCode,
    headers: {
      'Content-Type': 'application/json',
      'Access-Control-Allow-Origin': '*'
    },
    body: JSON.stringify(body)
  };
}

function errorResponse(statusCode, message) {
  return response(statusCode, { error: message });
}

module.exports = { response, errorResponse };
EOF
```

---

### Step 4: Environment Variables

```bash
cd ../../

cat > .env << EOF
# DynamoDB Tables
SIGNS_TABLE=tsl-signmap-TrafficSigns-dev
USERS_TABLE=tsl-signmap-Users-dev
VOTES_TABLE=tsl-signmap-Votes-dev

# S3 Buckets
IMAGES_BUCKET=tsl-signmap-images-$(aws sts get-caller-identity --query Account --output text)

# SQS
QUEUE_URL=https://sqs.us-east-1.amazonaws.com/$(aws sts get-caller-identity --query Account --output text)/tsl-signmap-image-processing

# Application Config
COINS_PER_SUBMISSION=10
COINS_PER_VOTE=1
DAILY_VOTE_LIMIT=5
EOF
```

---

### Step 5: Create IAM Execution Role

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

# Attach basic execution role
aws iam attach-role-policy \
  --role-name tsl-signmap-lambda-role \
  --policy-arn arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole

# Create custom policy
cat > lambda-permissions.json << 'EOF'
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Action": [
      "dynamodb:GetItem",
      "dynamodb:PutItem",
      "dynamodb:UpdateItem",
      "dynamodb:Query",
      "dynamodb:Scan",
      "s3:GetObject",
      "s3:PutObject",
      "s3:PutObjectAcl",
      "sqs:SendMessage",
      "sqs:ReceiveMessage",
      "sqs:DeleteMessage"
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

### Verification

```bash
# Check structure
tree backend/functions -L 2

# Check dependencies
cd backend/functions/sign-submit && npm list

# Check IAM role
aws iam get-role --role-name tsl-signmap-lambda-role
```

---

### Next Step

Continue with [Deploy Lambda Functions](../5.4.2-create-interface-enpoint/)
