---
title: "Backend & API Gateway Deployment"
date: 2026-07-10
weight: 4
chapter: false
---

In this section, we will package and deploy the backend source code (Node.js) to **AWS Lambda** and establish **Amazon API Gateway** as a secure routing API portal integrated with a **Cognito Authorizer**.

---
### Step 1: Create IAM Role for Lambda

Before deploying Lambda, we need an IAM Role to grant least-privilege permissions required by backend logic functions:
- Read/Write data on the 5 **DynamoDB** tables.
- Generate Presigned URLs and write objects to the **S3 Bucket**.
- Push messages to the **SQS** queue.
- Send notification emails via **Amazon SES**.
- Write execution logs to **CloudWatch Logs**.

You can create an IAM Role named `student-portal-lambda` through the IAM Console, or let the infrastructure script create it automatically.

Once created, obtain the IAM Role ARN:
```text
LAMBDA_ROLE_ARN=arn:aws:iam::<ACCOUNT_ID>:role/student-portal-lambda
```

---
### Step 2: Deploy Lambda Functions (Deploy Lambdas)

The system consists of 21 Lambda functions running independent microservice APIs (CRUD for students, teachers, grades, document uploads, and an offline worker dispatching email notifications).

To deploy all these functions, export the required configuration variables and execute the deployment script:

```bash
# 1. Configure environment variables
export LAMBDA_ROLE_ARN="arn:aws:iam::<ACCOUNT_ID>:role/student-portal-lambda"
export DOCUMENTS_BUCKET="student-documents-<yourname>"
export NOTIFICATION_QUEUE_URL="https://sqs.us-east-1.amazonaws.com/<ACCOUNT_ID>/student-notifications"
export FROM_EMAIL="your-verified-email@example.com"

# 2. Run the packaging and deployment script
bash scripts/deploy-lambdas.sh us-east-1
```

> [!TIP]
> Make sure the email set in `FROM_EMAIL` is **Verified** in **Amazon SES** (in Sandbox mode, both sender and recipient email addresses must be verified before email dispatch can succeed).

![AWS Lambda Console](/images/5-Workshop/student-portal/lambda.png)

---
### Step 3: Deploy & Configure API Gateway

To expose the Lambda functions to the React Frontend, we will configure a REST API Gateway that routes HTTP endpoints to corresponding Lambda integrations.

Execute the API Gateway deployment script:

```bash
# Export the User Pool ID recorded from the previous Cognito setup step
export USER_POOL_ID="us-east-1_xxxxxxxxx"

# Execute the API deployment script
bash scripts/deploy-apigateway.sh us-east-1
```

The script automatically sets up the following resources on AWS API Gateway:
1. Creates a REST API named `student-portal-api`.
2. Creates a **Cognito Authorizer** mapped to the designated Cognito User Pool.
3. Provisions resources and methods:
   - `/students`, `/students/{id}` -> Maps to Student CRUD Lambdas.
   - `/teachers`, `/teachers/{id}` -> Maps to Teacher CRUD Lambdas.
   - `/grades`, `/grades/{id}` -> Maps to Grades CRUD Lambdas.
   - `/materials/upload-url`, `/materials/metadata` -> Maps to Learning Materials Lambdas.
   - `/documents/upload-url`, `/documents/metadata` -> Maps to Student Documents Lambdas.
4. Binds the Cognito Authorizer to endpoints requiring authentication (e.g., POST, PUT, DELETE).
5. Enables **CORS** configurations globally.
6. Deploys the API to a stage named `prod`.

After execution, copy the **Invoke URL** output displayed on the terminal:
```text
https://xxxxxxxxxx.execute-api.us-east-1.amazonaws.com/prod
```
This is the API gateway endpoint that your React frontend application will call.

Go to **API Gateway Console → APIs** to confirm the `student-portal-api` was created successfully:
- **Protocol**: REST
- **Endpoint type**: Regional
- **Security policy**: TLS_1_0

![API Gateway Console - APIs List](/images/5-Workshop/student-portal/api-gateway-list.png)

![API Gateway Overview](/images/5-Workshop/student-portal/api-gateway-1.png)
![API Gateway Details](/images/5-Workshop/student-portal/api-gateway-2.png)
