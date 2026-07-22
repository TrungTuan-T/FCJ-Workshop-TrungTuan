---
title : "Create Gateway Endpoint"
date : 2026 
weight : 1
chapter : false
pre : " <b> 5.3.1 </b> "
---

### Objective
Create a Gateway Endpoint to connect VPC Cloud with Amazon S3.

---

### Step 1: Open VPC Console

```
AWS Console → VPC → Endpoints → Create Endpoint
```

[Amazon VPC Console](https://us-east-1.console.aws.amazon.com/vpc/home?region=us-east-1#Endpoints:)

{{% notice note %}}
You'll see 6 existing endpoints for AWS Systems Manager (SSM) - pre-created by CloudFormation.
{{% /notice %}}

![endpoint](/images/5-Workshop/5.3-S3-vpc/endpoints.png)

---

### Step 2: Configure Endpoint

| Setting | Value |
|---------|-------|
| **Name** | `s3-gwe` |
| **Service category** | AWS services |
| **Service** | `com.amazonaws.us-east-1.s3` (Type: Gateway) |
| **VPC** | VPC Cloud |
| **Route tables** | Select route table (not Main) |
| **Policy** | Full access |

**Screenshots:**

![endpoint](/images/5-Workshop/5.3-S3-vpc/create-s3-gwe1.png)

![endpoint](/images/5-Workshop/5.3-S3-vpc/services.png)

![endpoint](/images/5-Workshop/5.3-S3-vpc/vpc.png)

![endpoint](/images/5-Workshop/5.3-S3-vpc/policy.png)

---

### Step 3: Create

Click **Create endpoint** → Wait for success message

![endpoint](/images/5-Workshop/5.3-S3-vpc/complete.png)

---

### Verify

```bash
# CLI: List endpoints
aws ec2 describe-vpc-endpoints \
  --filters Name=vpc-id,Values=<VPC_CLOUD_ID> \
  --query 'VpcEndpoints[?ServiceName==`com.amazonaws.us-east-1.s3`].[VpcEndpointId,State]' \
  --output table

# Expected output:
# vpce-xxxxx | available
```

**Route Table will automatically have entry:**
```
Destination: pl-xxxxx (S3 prefix list)
Target: vpce-xxxxx (Gateway Endpoint)
```

---

### Next
[Test Gateway Endpoint](../5.3.2-test-gwe/)