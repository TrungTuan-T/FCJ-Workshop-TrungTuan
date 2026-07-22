---
title: "Monitoring & Logging"
date: 2026-07-10
weight: 4
chapter: false
---

### Tổng quan

Setup CloudWatch monitoring và logging cho TSL-SignMap API.

---

### Bước 1: Enable CloudWatch Logs

```bash
# Create log role
cat > api-logging-trust.json << 'EOF'
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Principal": { "Service": "apigateway.amazonaws.com" },
    "Action": "sts:AssumeRole"
  }]
}
EOF

aws iam create-role \
  --role-name APIGatewayLogsRole \
  --assume-role-policy-document file://api-logging-trust.json

aws iam attach-role-policy \
  --role-name APIGatewayLogsRole \
  --policy-arn arn:aws:iam::aws:policy/service-role/AmazonAPIGatewayPushToCloudWatchLogs

# Set account role
ROLE_ARN=$(aws iam get-role --role-name APIGatewayLogsRole --query 'Role.Arn' --output text)

aws apigateway update-account \
  --patch-operations op=replace,path=/cloudwatchRoleArn,value=$ROLE_ARN
```

---

### Bước 2: Configure Stage Logging

```bash
aws apigateway update-stage \
  --rest-api-id $API_ID \
  --stage-name prod \
  --patch-operations \
    op=replace,path=/accessLogSettings/destinationArn,value=arn:aws:logs:us-east-1:$(aws sts get-caller-identity --query Account --output text):log-group:tsl-api-access-logs \
    op=replace,path=/accessLogSettings/format,value='$context.requestId $context.extendedRequestId $context.identity.sourceIp $context.requestTime $context.routeKey $context.status' \
    op=replace,path=/*/*/logging/loglevel,value=INFO \
    op=replace,path=/*/*/logging/dataTrace,value=true \
    op=replace,path=/*/*/metrics/enabled,value=true

# Create log group
aws logs create-log-group --log-group-name tsl-api-access-logs
aws logs put-retention-policy \
  --log-group-name tsl-api-access-logs \
  --retention-in-days 7
```

---

### Bước 3: Create CloudWatch Dashboard

```bash
cat > dashboard.json << EOF
{
  "widgets": [
    {
      "type": "metric",
      "properties": {
        "metrics": [
          ["AWS/ApiGateway", "Count", {"stat": "Sum", "label": "Total Requests"}],
          [".", "4XXError", {"stat": "Sum", "label": "4XX Errors"}],
          [".", "5XXError", {"stat": "Sum", "label": "5XX Errors"}]
        ],
        "period": 300,
        "stat": "Average",
        "region": "us-east-1",
        "title": "API Gateway Metrics",
        "dimensions": {
          "ApiName": ["tsl-signmap-api"]
        }
      }
    },
    {
      "type": "metric",
      "properties": {
        "metrics": [
          ["AWS/ApiGateway", "Latency", {"stat": "Average"}],
          ["...", {"stat": "p99"}]
        ],
        "period": 300,
        "stat": "Average",
        "region": "us-east-1",
        "title": "API Latency",
        "dimensions": {
          "ApiName": ["tsl-signmap-api"]
        }
      }
    },
    {
      "type": "metric",
      "properties": {
        "metrics": [
          ["AWS/Lambda", "Invocations", {"stat": "Sum"}],
          [".", "Errors", {"stat": "Sum"}],
          [".", "Duration", {"stat": "Average"}]
        ],
        "period": 300,
        "stat": "Average",
        "region": "us-east-1",
        "title": "Lambda Metrics"
      }
    }
  ]
}
EOF

aws cloudwatch put-dashboard \
  --dashboard-name TSL-SignMap-API \
  --dashboard-body file://dashboard.json
```

---

### Bước 4: Setup Alarms

```bash
# High error rate alarm
aws cloudwatch put-metric-alarm \
  --alarm-name tsl-api-high-4xx \
  --alarm-description "Alert when 4XX error rate is high" \
  --metric-name 4XXError \
  --namespace AWS/ApiGateway \
  --statistic Sum \
  --period 300 \
  --threshold 10 \
  --comparison-operator GreaterThanThreshold \
  --evaluation-periods 2 \
  --dimensions Name=ApiName,Value=tsl-signmap-api

# High latency alarm
aws cloudwatch put-metric-alarm \
  --alarm-name tsl-api-high-latency \
  --alarm-description "Alert when API latency is high" \
  --metric-name Latency \
  --namespace AWS/ApiGateway \
  --statistic Average \
  --period 300 \
  --threshold 1000 \
  --comparison-operator GreaterThanThreshold \
  --evaluation-periods 2 \
  --dimensions Name=ApiName,Value=tsl-signmap-api

# Lambda errors
aws cloudwatch put-metric-alarm \
  --alarm-name tsl-lambda-errors \
  --alarm-description "Alert on Lambda errors" \
  --metric-name Errors \
  --namespace AWS/Lambda \
  --statistic Sum \
  --period 300 \
  --threshold 5 \
  --comparison-operator GreaterThanThreshold \
  --evaluation-periods 1
```

---

### Bước 5: Query Logs với CloudWatch Insights

```bash
# Query mẫu: Top error endpoints
aws logs start-query \
  --log-group-name tsl-api-access-logs \
  --start-time $(date -u -d '1 hour ago' +%s) \
  --end-time $(date -u +%s) \
  --query-string 'fields @timestamp, @message
    | filter @message like /ERROR/
    | stats count() by routeKey
    | sort count desc
    | limit 10'
```

**Query examples:**

```sql
-- Request latency distribution
fields @timestamp, routeKey, status, @duration
| filter status >= 200
| stats avg(@duration), max(@duration), pct(@duration, 95) by routeKey

-- Error analysis
fields @timestamp, @message, routeKey, status
| filter status >= 400
| stats count() by status, routeKey

-- User activity
fields @timestamp, sourceIp, routeKey
| stats count() by sourceIp
| sort count desc
| limit 20
```

---

### Bước 6: Enable X-Ray Tracing

```bash
# Enable tracing
aws apigateway update-stage \
  --rest-api-id $API_ID \
  --stage-name prod \
  --patch-operations op=replace,path=/tracingEnabled,value=true

# Update Lambda functions
for FUNC in sign-submit sign-query sign-vote; do
  aws lambda update-function-configuration \
    --function-name tsl-signmap-$FUNC \
    --tracing-config Mode=Active
done

# Grant X-Ray permissions
cat > xray-policy.json << 'EOF'
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Action": [
      "xray:PutTraceSegments",
      "xray:PutTelemetryRecords"
    ],
    "Resource": "*"
  }]
}
EOF

aws iam put-role-policy \
  --role-name tsl-signmap-lambda-role \
  --policy-name XRayPolicy \
  --policy-document file://xray-policy.json
```

---

### Verification

```bash
# View recent logs
aws logs tail tsl-api-access-logs --follow

# Check metrics
aws cloudwatch get-metric-statistics \
  --namespace AWS/ApiGateway \
  --metric-name Count \
  --dimensions Name=ApiName,Value=tsl-signmap-api \
  --start-time $(date -u -d '1 hour ago' --iso-8601=seconds) \
  --end-time $(date -u --iso-8601=seconds) \
  --period 300 \
  --statistics Sum

# View dashboard
echo "Dashboard: https://console.aws.amazon.com/cloudwatch/home?region=us-east-1#dashboards:name=TSL-SignMap-API"

# View X-Ray traces
echo "X-Ray: https://console.aws.amazon.com/xray/home?region=us-east-1#/service-map"
```

---

### Cost Estimate

| Service | Usage | Cost/month |
|---------|-------|------------|
| CloudWatch Logs | 5GB ingestion, 7 days retention | $2.50 |
| CloudWatch Metrics | 50 custom metrics | $0.30 |
| CloudWatch Alarms | 3 alarms | $0.30 |
| X-Ray | 1M requests traced | $5.00 |
| **Total** | | **$8.10/month** |

---

### Hoàn thành!

Bạn đã setup xong Backend API với đầy đủ monitoring và logging cho TSL-SignMap! 🎉
