---
title: "Setup API Gateway"
date: 2026-07-10
weight: 3
chapter: false
---

### Overview

Create REST API Gateway with Cognito Authorizer for TSL-SignMap.

---

### Step 1: Create REST API

```bash
API_ID=$(aws apigateway create-rest-api \
  --name tsl-signmap-api \
  --description "TSL-SignMap Backend API" \
  --endpoint-configuration types=REGIONAL \
  --query 'id' \
  --output text)

echo "API ID: $API_ID"

# Get root resource
ROOT_ID=$(aws apigateway get-resources \
  --rest-api-id $API_ID \
  --query 'items[0].id' \
  --output text)
```

---

### Step 2: Create Cognito Authorizer

```bash
# Get User Pool ARN
USER_POOL_ARN=$(aws cognito-idp describe-user-pool \
  --user-pool-id $USER_POOL_ID \
  --query 'UserPool.Arn' \
  --output text)

# Create authorizer
AUTHORIZER_ID=$(aws apigateway create-authorizer \
  --rest-api-id $API_ID \
  --name TSLCognitoAuthorizer \
  --type COGNITO_USER_POOLS \
  --provider-arns $USER_POOL_ARN \
  --identity-source method.request.header.Authorization \
  --query 'id' \
  --output text)

echo "Authorizer ID: $AUTHORIZER_ID"
```

---

### Step 3: Create Resources & Methods

#### /signs Resource

```bash
# Create /signs resource
SIGNS_ID=$(aws apigateway create-resource \
  --rest-api-id $API_ID \
  --parent-id $ROOT_ID \
  --path-part signs \
  --query 'id' \
  --output text)

# POST /signs method
aws apigateway put-method \
  --rest-api-id $API_ID \
  --resource-id $SIGNS_ID \
  --http-method POST \
  --authorization-type COGNITO_USER_POOLS \
  --authorizer-id $AUTHORIZER_ID

# Integration with Lambda
aws apigateway put-integration \
  --rest-api-id $API_ID \
  --resource-id $SIGNS_ID \
  --http-method POST \
  --type AWS_PROXY \
  --integration-http-method POST \
  --uri "arn:aws:apigateway:us-east-1:lambda:path/2015-03-31/functions/arn:aws:lambda:us-east-1:$(aws sts get-caller-identity --query Account --output text):function:tsl-signmap-sign-submit/invocations"

# Grant permission
aws lambda add-permission \
  --function-name tsl-signmap-sign-submit \
  --statement-id apigateway-post-signs \
  --action lambda:InvokeFunction \
  --principal apigateway.amazonaws.com \
  --source-arn "arn:aws:execute-api:us-east-1:$(aws sts get-caller-identity --query Account --output text):$API_ID/*/POST/signs"
```

#### /signs/nearby Resource

```bash
# Create /signs/nearby
NEARBY_ID=$(aws apigateway create-resource \
  --rest-api-id $API_ID \
  --parent-id $SIGNS_ID \
  --path-part nearby \
  --query 'id' \
  --output text)

# GET method
aws apigateway put-method \
  --rest-api-id $API_ID \
  --resource-id $NEARBY_ID \
  --http-method GET \
  --authorization-type COGNITO_USER_POOLS \
  --authorizer-id $AUTHORIZER_ID \
  --request-parameters method.request.querystring.lat=true,method.request.querystring.lng=true,method.request.querystring.radius=false

# Integration
aws apigateway put-integration \
  --rest-api-id $API_ID \
  --resource-id $NEARBY_ID \
  --http-method GET \
  --type AWS_PROXY \
  --integration-http-method POST \
  --uri "arn:aws:apigateway:us-east-1:lambda:path/2015-03-31/functions/arn:aws:lambda:us-east-1:$(aws sts get-caller-identity --query Account --output text):function:tsl-signmap-sign-query/invocations"

aws lambda add-permission \
  --function-name tsl-signmap-sign-query \
  --statement-id apigateway-get-nearby \
  --action lambda:InvokeFunction \
  --principal apigateway.amazonaws.com
```

#### /votes Resource

```bash
# Create /votes
VOTES_ID=$(aws apigateway create-resource \
  --rest-api-id $API_ID \
  --parent-id $ROOT_ID \
  --path-part votes \
  --query 'id' \
  --output text)

# POST method
aws apigateway put-method \
  --rest-api-id $API_ID \
  --resource-id $VOTES_ID \
  --http-method POST \
  --authorization-type COGNITO_USER_POOLS \
  --authorizer-id $AUTHORIZER_ID

# Integration
aws apigateway put-integration \
  --rest-api-id $API_ID \
  --resource-id $VOTES_ID \
  --http-method POST \
  --type AWS_PROXY \
  --integration-http-method POST \
  --uri "arn:aws:apigateway:us-east-1:lambda:path/2015-03-31/functions/arn:aws:lambda:us-east-1:$(aws sts get-caller-identity --query Account --output text):function:tsl-signmap-sign-vote/invocations"

aws lambda add-permission \
  --function-name tsl-signmap-sign-vote \
  --statement-id apigateway-post-votes \
  --action lambda:InvokeFunction \
  --principal apigateway.amazonaws.com
```

---

### Step 4: Deploy API

```bash
# Create deployment
aws apigateway create-deployment \
  --rest-api-id $API_ID \
  --stage-name prod \
  --description "Production deployment"

# Get API URL
API_URL="https://$API_ID.execute-api.us-east-1.amazonaws.com/prod"
echo "API URL: $API_URL"
```

---

### Step 5: Enable CORS

```bash
# Enable CORS for all resources
for RESOURCE_ID in $SIGNS_ID $NEARBY_ID $VOTES_ID; do
  aws apigateway put-method \
    --rest-api-id $API_ID \
    --resource-id $RESOURCE_ID \
    --http-method OPTIONS \
    --authorization-type NONE

  aws apigateway put-integration \
    --rest-api-id $API_ID \
    --resource-id $RESOURCE_ID \
    --http-method OPTIONS \
    --type MOCK \
    --request-templates '{"application/json":"{\"statusCode\": 200}"}'

  aws apigateway put-method-response \
    --rest-api-id $API_ID \
    --resource-id $RESOURCE_ID \
    --http-method OPTIONS \
    --status-code 200 \
    --response-parameters \
      method.response.header.Access-Control-Allow-Headers=true,\
      method.response.header.Access-Control-Allow-Methods=true,\
      method.response.header.Access-Control-Allow-Origin=true

  aws apigateway put-integration-response \
    --rest-api-id $API_ID \
    --resource-id $RESOURCE_ID \
    --http-method OPTIONS \
    --status-code 200 \
    --response-parameters \
      method.response.header.Access-Control-Allow-Headers="'Content-Type,Authorization'",\
      method.response.header.Access-Control-Allow-Methods="'GET,POST,PUT,DELETE,OPTIONS'",\
      method.response.header.Access-Control-Allow-Origin="'*'"
done

# Redeploy
aws apigateway create-deployment \
  --rest-api-id $API_ID \
  --stage-name prod
```

---

### Testing

```bash
# Get token
TOKEN=$(aws cognito-idp initiate-auth \
  --client-id $CLIENT_ID \
  --auth-flow USER_PASSWORD_AUTH \
  --auth-parameters USERNAME=admin@tsl-signmap.com,PASSWORD=YourPassword \
  --query 'AuthenticationResult.IdToken' \
  --output text)

# Test POST /signs
curl -X POST $API_URL/signs \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "location": {"lat": 10.762622, "lng": 106.660172},
    "signType": "stop",
    "imageKey": "signs/test/image.jpg"
  }'

# Test GET /signs/nearby
curl "$API_URL/signs/nearby?lat=10.76&lng=106.66&radius=5" \
  -H "Authorization: Bearer $TOKEN"

# Test POST /votes
curl -X POST $API_URL/votes \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"signId": "sign_123", "voteType": "upvote"}'
```

---

### Next Step

Continue with [Configure API Monitoring](../5.4.4-dns-simulation/)
