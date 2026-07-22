---
title: "Deploy Lambda Functions"
date: 2026-07-10
weight: 2
chapter: false
---

### Tổng quan

Deploy 7 Lambda functions cho backend TSL-SignMap.

---

### Bước 1: sign-submit Function

```bash
cd backend/functions/sign-submit

cat > index.js << 'EOF'
const AWS = require('aws-sdk');
const { v4: uuidv4 } = require('uuid');
const geohash = require('geohash');

const dynamodb = new AWS.DynamoDB.DocumentClient();
const sqs = new AWS.SQS();

exports.handler = async (event) => {
  try {
    const body = JSON.parse(event.body);
    const { location, signType, imageKey } = body;
    const userId = event.requestContext.authorizer.claims.sub;
    
    const signId = `sign_${uuidv4()}`;
    const geoHash = geohash.encode(location.lat, location.lng, 8);
    
    const sign = {
      SignID: signId,
      UserID: userId,
      Location: location,
      GeoHash: geoHash,
      SignType: signType,
      ImageKey: imageKey,
      Status: 'pending',
      UpvoteCount: 0,
      DownvoteCount: 0,
      CreatedAt: new Date().toISOString()
    };
    
    // Save to DynamoDB
    await dynamodb.put({
      TableName: process.env.SIGNS_TABLE,
      Item: sign
    }).promise();
    
    // Send to SQS for AI processing
    await sqs.sendMessage({
      QueueUrl: process.env.QUEUE_URL,
      MessageBody: JSON.stringify({ signId, imageKey })
    }).promise();
    
    // Award coins
    await dynamodb.update({
      TableName: process.env.USERS_TABLE,
      Key: { UserID: userId },
      UpdateExpression: 'ADD CoinBalance :coins',
      ExpressionAttributeValues: {
        ':coins': parseInt(process.env.COINS_PER_SUBMISSION || 10)
      }
    }).promise();
    
    return {
      statusCode: 201,
      headers: { 'Access-Control-Allow-Origin': '*' },
      body: JSON.stringify({ signId, message: 'Sign submitted successfully' })
    };
  } catch (error) {
    console.error(error);
    return {
      statusCode: 500,
      body: JSON.stringify({ error: error.message })
    };
  }
};
EOF

# Deploy
zip -r function.zip index.js node_modules/

ROLE_ARN=$(aws iam get-role --role-name tsl-signmap-lambda-role --query 'Role.Arn' --output text)

aws lambda create-function \
  --function-name tsl-signmap-sign-submit \
  --runtime nodejs18.x \
  --role $ROLE_ARN \
  --handler index.handler \
  --zip-file fileb://function.zip \
  --timeout 30 \
  --memory-size 512 \
  --environment Variables="{
    SIGNS_TABLE=tsl-signmap-TrafficSigns-dev,
    USERS_TABLE=tsl-signmap-Users-dev,
    QUEUE_URL=$QUEUE_URL,
    COINS_PER_SUBMISSION=10
  }"
```

---

### Bước 2: sign-query Function

```bash
cd ../sign-query

cat > index.js << 'EOF'
const AWS = require('aws-sdk');
const geohash = require('geohash');

const dynamodb = new AWS.DynamoDB.DocumentClient();

exports.handler = async (event) => {
  try {
    const { lat, lng, radius = 1 } = event.queryStringParameters;
    
    // Get geohash precision based on radius
    const precision = radius <= 1 ? 7 : radius <= 5 ? 6 : 5;
    const centerHash = geohash.encode(parseFloat(lat), parseFloat(lng), precision);
    
    // Query by geohash
    const result = await dynamodb.query({
      TableName: process.env.SIGNS_TABLE,
      IndexName: 'GeoHash-index',
      KeyConditionExpression: 'begins_with(GeoHash, :hash)',
      ExpressionAttributeValues: {
        ':hash': centerHash.substring(0, precision - 1)
      }
    }).promise();
    
    // Filter by actual distance
    const signs = result.Items.filter(sign => {
      const distance = calculateDistance(
        parseFloat(lat), parseFloat(lng),
        sign.Location.lat, sign.Location.lng
      );
      return distance <= parseFloat(radius);
    });
    
    return {
      statusCode: 200,
      headers: { 'Access-Control-Allow-Origin': '*' },
      body: JSON.stringify({ signs, count: signs.length })
    };
  } catch (error) {
    console.error(error);
    return {
      statusCode: 500,
      body: JSON.stringify({ error: error.message })
    };
  }
};

function calculateDistance(lat1, lon1, lat2, lon2) {
  const R = 6371; // Earth radius in km
  const dLat = (lat2 - lat1) * Math.PI / 180;
  const dLon = (lon2 - lon1) * Math.PI / 180;
  const a = Math.sin(dLat/2) * Math.sin(dLat/2) +
    Math.cos(lat1 * Math.PI / 180) * Math.cos(lat2 * Math.PI / 180) *
    Math.sin(dLon/2) * Math.sin(dLon/2);
  return R * 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1-a));
}
EOF

zip -r function.zip index.js node_modules/

aws lambda create-function \
  --function-name tsl-signmap-sign-query \
  --runtime nodejs18.x \
  --role $ROLE_ARN \
  --handler index.handler \
  --zip-file fileb://function.zip \
  --timeout 15 \
  --memory-size 256 \
  --environment Variables="{SIGNS_TABLE=tsl-signmap-TrafficSigns-dev}"
```

---

### Bước 3: sign-vote Function

```bash
cd ../sign-vote

cat > index.js << 'EOF'
const AWS = require('aws-sdk');
const { v4: uuidv4 } = require('uuid');

const dynamodb = new AWS.DynamoDB.DocumentClient();

exports.handler = async (event) => {
  try {
    const body = JSON.parse(event.body);
    const { signId, voteType } = body; // upvote or downvote
    const userId = event.requestContext.authorizer.claims.sub;
    
    // Check if already voted
    const existingVote = await dynamodb.query({
      TableName: process.env.VOTES_TABLE,
      IndexName: 'SignID-index',
      KeyConditionExpression: 'SignID = :sid',
      FilterExpression: 'UserID = :uid',
      ExpressionAttributeValues: {
        ':sid': signId,
        ':uid': userId
      }
    }).promise();
    
    if (existingVote.Items.length > 0) {
      return {
        statusCode: 409,
        body: JSON.stringify({ error: 'Already voted on this sign' })
      };
    }
    
    // Record vote
    await dynamodb.put({
      TableName: process.env.VOTES_TABLE,
      Item: {
        VoteID: `vote_${uuidv4()}`,
        SignID: signId,
        UserID: userId,
        VoteType: voteType,
        CreatedAt: new Date().toISOString()
      }
    }).promise();
    
    // Update sign counts
    const updateExpr = voteType === 'upvote' 
      ? 'ADD UpvoteCount :val' 
      : 'ADD DownvoteCount :val';
      
    await dynamodb.update({
      TableName: process.env.SIGNS_TABLE,
      Key: { SignID: signId },
      UpdateExpression: updateExpr,
      ExpressionAttributeValues: { ':val': 1 }
    }).promise();
    
    // Award coins to voter
    await dynamodb.update({
      TableName: process.env.USERS_TABLE,
      Key: { UserID: userId },
      UpdateExpression: 'ADD CoinBalance :coins',
      ExpressionAttributeValues: { ':coins': 1 }
    }).promise();
    
    return {
      statusCode: 200,
      headers: { 'Access-Control-Allow-Origin': '*' },
      body: JSON.stringify({ message: 'Vote recorded' })
    };
  } catch (error) {
    console.error(error);
    return {
      statusCode: 500,
      body: JSON.stringify({ error: error.message })
    };
  }
};
EOF

zip -r function.zip index.js node_modules/

aws lambda create-function \
  --function-name tsl-signmap-sign-vote \
  --runtime nodejs18.x \
  --role $ROLE_ARN \
  --handler index.handler \
  --zip-file fileb://function.zip \
  --timeout 15 \
  --environment Variables="{
    SIGNS_TABLE=tsl-signmap-TrafficSigns-dev,
    USERS_TABLE=tsl-signmap-Users-dev,
    VOTES_TABLE=tsl-signmap-Votes-dev
  }"
```

---

### Verification

```bash
# List functions
aws lambda list-functions | grep tsl-signmap

# Test sign-query
aws lambda invoke \
  --function-name tsl-signmap-sign-query \
  --payload '{"queryStringParameters":{"lat":"10.76","lng":"106.66","radius":"5"}}' \
  response.json

cat response.json
```

---

### Next Step

Tiếp tục với [Setup API Gateway](../5.4.3-test-endpoint/)
