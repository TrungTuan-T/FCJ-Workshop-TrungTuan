---
title: "Frontend Deployment"
date: 2026-07-10
weight: 5
chapter: false
---

### Overview

Deploy React mobile web app to S3 + CloudFront with Cognito authentication.

**Tech Stack:**
- React 18 + TypeScript
- AWS Amplify
- Mapbox/OpenStreetMap
- Material-UI

---

### Step 1: Setup Frontend

```bash
cd frontend

# Install dependencies
npm install

# Configure Amplify
cat > src/aws-config.js << EOF
export const awsConfig = {
  Auth: {
    region: 'us-east-1',
    userPoolId: '${USER_POOL_ID}',
    userPoolWebClientId: '${CLIENT_ID}'
  },
  API: {
    endpoints: [{
      name: 'TSLSignMapAPI',
      endpoint: 'https://${API_ID}.execute-api.us-east-1.amazonaws.com/prod'
    }]
  }
};
EOF
```

---

### Step 2: Build Frontend

```bash
# Build production
npm run build

# Output in build/ directory
ls -lh build/
```

**Build output:**
```
build/
├── index.html
├── static/
│   ├── js/
│   ├── css/
│   └── media/
└── manifest.json
```

---

### Step 3: Deploy to S3

```bash
FRONTEND_BUCKET="tsl-signmap-frontend-$(aws sts get-caller-identity --query Account --output text)"

# Create bucket
aws s3 mb s3://$FRONTEND_BUCKET

# Enable static website hosting
aws s3 website s3://$FRONTEND_BUCKET \
  --index-document index.html \
  --error-document index.html

# Upload files
aws s3 sync build/ s3://$FRONTEND_BUCKET/ \
  --delete \
  --cache-control "public, max-age=31536000, immutable" \
  --exclude "*.html" \
  --exclude "service-worker.js"

# Upload HTML with no-cache
aws s3 sync build/ s3://$FRONTEND_BUCKET/ \
  --exclude "*" \
  --include "*.html" \
  --include "service-worker.js" \
  --cache-control "no-cache, no-store, must-revalidate"
```

---

### Step 4: Setup CloudFront

```bash
# Create OAI
OAI_ID=$(aws cloudfront create-cloud-front-origin-access-identity \
  --cloud-front-origin-access-identity-config \
    CallerReference=$(date +%s),Comment="TSL-SignMap OAI" \
  --query 'CloudFrontOriginAccessIdentity.Id' \
  --output text)

# Create distribution
cat > cloudfront-config.json << EOF
{
  "Comment": "TSL-SignMap Frontend",
  "Enabled": true,
  "Origins": {
    "Quantity": 1,
    "Items": [{
      "Id": "S3-tsl-signmap",
      "DomainName": "${FRONTEND_BUCKET}.s3.amazonaws.com",
      "S3OriginConfig": {
        "OriginAccessIdentity": "origin-access-identity/cloudfront/${OAI_ID}"
      }
    }]
  },
  "DefaultRootObject": "index.html",
  "DefaultCacheBehavior": {
    "TargetOriginId": "S3-tsl-signmap",
    "ViewerProtocolPolicy": "redirect-to-https",
    "AllowedMethods": {
      "Quantity": 2,
      "Items": ["GET", "HEAD"]
    },
    "Compress": true,
    "MinTTL": 0,
    "DefaultTTL": 86400,
    "MaxTTL": 31536000
  },
  "CustomErrorResponses": {
    "Quantity": 1,
    "Items": [{
      "ErrorCode": 404,
      "ResponsePagePath": "/index.html",
      "ResponseCode": "200",
      "ErrorCachingMinTTL": 300
    }]
  }
}
EOF

DISTRIBUTION_ID=$(aws cloudfront create-distribution \
  --distribution-config file://cloudfront-config.json \
  --query 'Distribution.Id' \
  --output text)

# Get CloudFront domain
DOMAIN=$(aws cloudfront get-distribution \
  --id $DISTRIBUTION_ID \
  --query 'Distribution.DomainName' \
  --output text)

echo "Frontend URL: https://$DOMAIN"
```

---

### Step 5: Update S3 Bucket Policy

```bash
cat > bucket-policy.json << EOF
{
  "Version": "2012-10-17",
  "Statement": [{
    "Sid": "AllowCloudFrontOAI",
    "Effect": "Allow",
    "Principal": {
      "AWS": "arn:aws:iam::cloudfront:user/CloudFront Origin Access Identity ${OAI_ID}"
    },
    "Action": "s3:GetObject",
    "Resource": "arn:aws:s3:::${FRONTEND_BUCKET}/*"
  }]
}
EOF

aws s3api put-bucket-policy \
  --bucket $FRONTEND_BUCKET \
  --policy file://bucket-policy.json
```

---

### Frontend Features

**1. Authentication**
```javascript
// Login
import { Auth } from 'aws-amplify';

async function login(email, password) {
  const user = await Auth.signIn(email, password);
  return user;
}

// Get current user
const user = await Auth.currentAuthenticatedUser();
const token = user.signInUserSession.idToken.jwtToken;
```

**2. Submit Sign**
```javascript
// Get upload URL
const response = await fetch(
  `${API_URL}/signs/upload-url?filename=sign.jpg`,
  { headers: { Authorization: `Bearer ${token}` } }
);
const { uploadUrl, imageKey } = await response.json();

// Upload image to S3
await fetch(uploadUrl, {
  method: 'PUT',
  body: imageFile,
  headers: { 'Content-Type': 'image/jpeg' }
});

// Submit sign metadata
await fetch(`${API_URL}/signs`, {
  method: 'POST',
  headers: {
    'Authorization': `Bearer ${token}`,
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    location: { lat, lng },
    signType: 'stop',
    imageKey
  })
});
```

**3. Map Integration**
```javascript
import mapboxgl from 'mapbox-gl';

const map = new mapboxgl.Map({
  container: 'map',
  style: 'mapbox://styles/mapbox/streets-v11',
  center: [lng, lat],
  zoom: 13
});

// Load nearby signs
const response = await fetch(
  `${API_URL}/signs/nearby?lat=${lat}&lng=${lng}&radius=2`,
  { headers: { Authorization: `Bearer ${token}` } }
);
const signs = await response.json();

// Add markers
signs.forEach(sign => {
  new mapboxgl.Marker()
    .setLngLat([sign.location.lng, sign.location.lat])
    .setPopup(new mapboxgl.Popup().setHTML(`<h3>${sign.signType}</h3>`))
    .addTo(map);
});
```

---

### Testing

```bash
# Test locally
npm start

# Access at http://localhost:3000

# Test production build
npm run build
npx serve -s build -p 3000
```

**Manual Testing:**
1. Navigate to CloudFront URL
2. Register new account
3. Verify email
4. Login
5. Submit traffic sign with photo
6. View on map
7. Vote on other signs
8. Check coin balance

---

### CI/CD with GitHub Actions

```yaml
# .github/workflows/deploy.yml
name: Deploy Frontend

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      
      - name: Setup Node.js
        uses: actions/setup-node@v2
        with:
          node-version: '18'
      
      - name: Install dependencies
        run: npm ci
        working-directory: ./frontend
      
      - name: Build
        run: npm run build
        working-directory: ./frontend
      
      - name: Deploy to S3
        run: |
          aws s3 sync build/ s3://${{ secrets.FRONTEND_BUCKET }}/ --delete
        working-directory: ./frontend
      
      - name: Invalidate CloudFront
        run: |
          aws cloudfront create-invalidation \
            --distribution-id ${{ secrets.DISTRIBUTION_ID }} \
            --paths "/*"
```

---

### Performance Optimization

| Technique | Implementation |
|-----------|----------------|
| Code splitting | React.lazy() for routes |
| Image optimization | WebP format, lazy loading |
| Caching | CloudFront + Service Worker |
| Compression | Gzip/Brotli enabled |
| CDN | CloudFront edge locations |

---

### Cost Estimate

| Service | Usage | Cost/month |
|---------|-------|------------|
| S3 | 100MB storage, 100K requests | $0.50 |
| CloudFront | 50GB transfer, 1M requests | $4.50 |
| Route 53 (optional) | Hosted zone | $0.50 |
| **Total** | | **$5.50/month** |

---

### Next: Testing

```bash
cd ../5.6-testing-cleanup/
```
