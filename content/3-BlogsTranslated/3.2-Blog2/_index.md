---
title: "Blog 2"
date: 2026
weight: 1
chapter: false
pre: " <b> 3.2. </b> "
---

# Building Mobile Backend for Crowdsourcing Apps with AWS Serverless

Crowdsourcing applications like TSL-SignMap require a scalable, real-time, and cost-effective backend. This article presents a serverless architecture for mobile apps managing location-based data.

---

## Crowdsourcing App Requirements

| Requirement | AWS Solution |
|-------------|--------------|
| User authentication | Amazon Cognito |
| Real-time location | Amazon Location Service |
| Image upload | S3 presigned URL |
| API scalability | API Gateway + Lambda |
| Data storage | DynamoDB (location GSI) |
| Push notifications | Amazon SNS |
| Offline sync | AppSync + DynamoDB |

---

## Architecture Overview

```
Mobile App → API Gateway → Lambda → DynamoDB
              ↓                  ↓
          Cognito         S3 (Images)
              ↓                  ↓
         Location Service    SageMaker
              ↓
            SNS (Notifications)
```

---

## User Authentication with Cognito

**Setup Cognito User Pool**

```bash
aws cognito-idp create-user-pool \
  --pool-name tsl-signmap-users \
  --policies "PasswordPolicy={MinimumLength=8,RequireUppercase=true}" \
  --auto-verified-attributes email \
  --mfa-configuration OPTIONAL
```

**Mobile App Integration (React Native)**

```javascript
import { Amplify, Auth } from 'aws-amplify';

Amplify.configure({
  Auth: {
    region: 'ap-southeast-1',
    userPoolId: 'ap-southeast-1_ABC123',
    userPoolWebClientId: 'abc123def456',
  }
});

// Sign up
async function signUp(email, password) {
  try {
    await Auth.signUp({
      username: email,
      password,
      attributes: { email }
    });
  } catch (error) {
    console.error('Sign up error:', error);
  }
}

// Sign in
async function signIn(email, password) {
  try {
    const user = await Auth.signIn(email, password);
    const token = user.signInUserSession.idToken.jwtToken;
    return token;
  } catch (error) {
    console.error('Sign in error:', error);
  }
}
```

**JWT Authorization in API Gateway**

```javascript
// Lambda Authorizer
exports.handler = async (event) => {
  const token = event.authorizationToken;
  
  try {
    const decoded = await verifyToken(token);
    return generatePolicy(decoded.sub, 'Allow', event.methodArn);
  } catch (error) {
    return generatePolicy('user', 'Deny', event.methodArn);
  }
};
```

---

## Location Service Integration

**Geocoding and Reverse Geocoding**

```javascript
// Lambda function for location processing
const { LocationClient, SearchPlaceIndexForPositionCommand } = require('@aws-sdk/client-location');

const client = new LocationClient({ region: 'ap-southeast-1' });

async function getAddress(lat, lng) {
  const command = new SearchPlaceIndexForPositionCommand({
    IndexName: 'TSL-SignMap-Index',
    Position: [lng, lat],
    MaxResults: 1
  });
  
  const response = await client.send(command);
  return response.Results[0].Place.Label;
}

// API response
{
  "lat": 10.762622,
  "lng": 106.660172,
  "address": "123 Nguyen Hue, District 1, HCMC"
}
```

---

## Image Upload with S3 Presigned URL

**Lambda Generate Presigned URL**

```python
import boto3
import json
from datetime import timedelta

s3 = boto3.client('s3')

def lambda_handler(event, context):
    user_id = event['requestContext']['authorizer']['claims']['sub']
    filename = event['queryStringParameters']['filename']
    content_type = event['queryStringParameters']['contentType']
    
    # Generate unique key
    key = f'signs/{user_id}/{filename}'
    
    # Create presigned URL (valid 5 minutes)
    presigned_url = s3.generate_presigned_url(
        'put_object',
        Params={
            'Bucket': 'tsl-signmap-images',
            'Key': key,
            'ContentType': content_type
        },
        ExpiresIn=300
    )
    
    return {
        'statusCode': 200,
        'body': json.dumps({
            'uploadUrl': presigned_url,
            'imageKey': key
        })
    }
```

**Mobile App Upload**

```javascript
// Get presigned URL
const response = await fetch(
  `https://api.tsl-signmap.com/signs/upload-url?filename=${filename}&contentType=image/jpeg`,
  {
    headers: { 'Authorization': `Bearer ${token}` }
  }
);

const { uploadUrl, imageKey } = await response.json();

// Upload image directly to S3
await fetch(uploadUrl, {
  method: 'PUT',
  headers: { 'Content-Type': 'image/jpeg' },
  body: imageBlob
});

// Save sign data with image key
await submitSign({
  location: { lat, lng },
  imageKey,
  signType: 'stop'
});
```

---

## DynamoDB Schema for Location Data

**Table Design**

```python
# TrafficSigns table
{
  "SignID": "sign_12345",  # PK
  "Location": {
    "lat": 10.762622,
    "lng": 106.660172
  },
  "GeoHash": "w3gvk1hs",  # For proximity search
  "SignType": "stop",
  "ImageKey": "signs/user123/image.jpg",
  "Status": "pending",
  "SubmittedBy": "user_abc123",
  "SubmittedAt": "2026-01-15T10:30:00Z",
  "Votes": {
    "upvotes": 5,
    "downvotes": 1
  }
}

# GSI for location queries
GSI: GeoHashIndex
- PartitionKey: GeoHash (first 5 chars)
- SortKey: SignID
```

**Proximity Search**

```python
import geohash2
import boto3

dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table('TrafficSigns')

def find_nearby_signs(lat, lng, radius_km=1):
    # Calculate geohash
    center_hash = geohash2.encode(lat, lng, precision=5)
    
    # Get neighbors
    neighbors = geohash2.neighbors(center_hash)
    all_hashes = [center_hash] + neighbors
    
    # Query DynamoDB
    signs = []
    for gh in all_hashes:
        response = table.query(
            IndexName='GeoHashIndex',
            KeyConditionExpression='GeoHash = :gh',
            ExpressionAttributeValues={':gh': gh}
        )
        signs.extend(response['Items'])
    
    # Filter by distance
    results = []
    for sign in signs:
        distance = calculate_distance(
            lat, lng,
            sign['Location']['lat'], sign['Location']['lng']
        )
        if distance <= radius_km:
            results.append({**sign, 'distance': distance})
    
    return sorted(results, key=lambda x: x['distance'])
```

---

## Real-time Notifications with SNS

**Create Topic and Subscribe**

```python
import boto3

sns = boto3.client('sns')

# Create topic
topic_arn = sns.create_topic(Name='sign-updates')['TopicArn']

# Subscribe user endpoint
sns.subscribe(
    TopicArn=topic_arn,
    Protocol='application',
    Endpoint=device_token  # From mobile app
)
```

**Send Notification When Sign is Approved**

```python
# Lambda trigger after admin approval
def lambda_handler(event, context):
    sign_id = event['signId']
    user_id = event['submittedBy']
    
    # Get user's SNS endpoint
    endpoint_arn = get_user_endpoint(user_id)
    
    # Send notification
    sns.publish(
        TargetArn=endpoint_arn,
        Message=json.dumps({
            'default': 'Your sign contribution was approved!',
            'GCM': json.dumps({
                'notification': {
                    'title': 'Sign Approved',
                    'body': f'Sign {sign_id} earned you 10 coins!'
                },
                'data': {
                    'signId': sign_id,
                    'coinsEarned': 10
                }
            })
        }),
        MessageStructure='json'
    )
```

---

## Cost Estimation

| Service | Usage | Cost/month |
|---------|-------|------------|
| API Gateway | 5M requests | $17.50 |
| Lambda | 10M invocations, 512MB | $15 |
| DynamoDB | 5M reads, 1M writes | $7.50 |
| S3 | 100K images, 50GB | $1.50 |
| Cognito | 5K MAU | $0 (free tier) |
| Location Service | 100K geocoding | $5 |
| SNS | 1M notifications | $2 |
| **Total** | | **~$48.50** |

---

## Conclusion

AWS serverless architecture provides an ideal solution for crowdsourcing mobile apps:
- **Auto-scaling**: Handle traffic spikes seamlessly
- **Cost-effective**: Pay per use, no idle costs
- **Developer-friendly**: Focus on business logic
- **Global**: Easy multi-region deployment

**References:**
- <https://docs.aws.amazon.com/location/>
- <https://docs.amplify.aws/lib/datastore/>

---
