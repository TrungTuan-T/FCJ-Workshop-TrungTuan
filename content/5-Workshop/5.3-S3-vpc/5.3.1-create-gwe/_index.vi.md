---
title : "Tạo Gateway Endpoint"
date : 2026 
weight : 1
chapter : false
pre : " <b> 5.3.1 </b> "
---

### Mục tiêu
Tạo Gateway Endpoint để kết nối VPC Cloud với Amazon S3.

---

### Bước 1: Mở VPC Console

```
AWS Console → VPC → Endpoints → Create Endpoint
```

[Amazon VPC Console](https://us-east-1.console.aws.amazon.com/vpc/home?region=us-east-1#Endpoints:)

{{% notice note %}}
Bạn sẽ thấy 6 endpoints cho AWS Systems Manager (SSM) - đã được CloudFormation tạo sẵn.
{{% /notice %}}

![endpoint](/images/5-Workshop/5.3-S3-vpc/endpoints.png)

---

### Bước 2: Configure Endpoint

| Setting | Value |
|---------|-------|
| **Name** | `s3-gwe` |
| **Service category** | AWS services |
| **Service** | `com.amazonaws.us-east-1.s3` (Type: Gateway) |
| **VPC** | VPC Cloud |
| **Route tables** | Chọn route table (không phải Main) |
| **Policy** | Full access |

**Screenshots:**

![endpoint](/images/5-Workshop/5.3-S3-vpc/create-s3-gwe1.png)

![endpoint](/images/5-Workshop/5.3-S3-vpc/services.png)

![endpoint](/images/5-Workshop/5.3-S3-vpc/vpc.png)

![endpoint](/images/5-Workshop/5.3-S3-vpc/policy.png)

---

### Bước 3: Create

Click **Create endpoint** → Đợi thông báo thành công

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

**Route Table sẽ tự động có entry:**
```
Destination: pl-xxxxx (S3 prefix list)
Target: vpce-xxxxx (Gateway Endpoint)
```

---

### Next
[Test Gateway Endpoint](../5.3.2-test-gwe/)
