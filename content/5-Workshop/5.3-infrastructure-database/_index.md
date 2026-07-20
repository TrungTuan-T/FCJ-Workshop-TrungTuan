---
title: "Database & Infrastructure"
date: 2026-07-10
weight: 3
chapter: false
---

In this section, we will provision the core infrastructure services for data storage, user authentication, and message queuing, including: **DynamoDB**, **S3 Bucket**, **SQS Queue**, and **Amazon Cognito**.

The project provides pre-configured scripts to automate this setup process.

---
### Step 1: Install Dependencies (npm packages)

Navigate to the project root directory on your computer and install the required libraries for both backend and frontend:

```bash
# 1. Navigate to the backend directory and install dependencies
cd backend
npm install

# 2. Navigate to the frontend directory and install dependencies
cd ../frontend
npm install
```

---
### Step 2: Create DynamoDB Tables

The Student Management system uses **6 DynamoDB tables** for isolated business data storage, all using `id (S)` as Partition Key and running in **On-Demand (PAY_PER_REQUEST)** billing mode:

| Table | Partition Key | Description |
| :--- | :--- | :--- |
| **Students** | `id` (String) | Student profile (studentId) |
| **Teachers** | `id` (String) | Teacher profile (teacherId) |
| **Classes** | `id` (String) | Class information |
| **Grades** | `id` (String) | Subject grades (`${studentId}-${subject}-${timestamp}`) |
| **Materials** | `id` (String) | Teacher learning materials (`${type}-${timestamp}`) |
| **Documents** | `id` (String) | Student document metadata (`${studentId}-${timestamp}`) |

Run the following script to automatically create all tables on AWS with `PAY_PER_REQUEST` (On-Demand) billing:

```bash
cd ..
bash scripts/deploy-dynamodb.sh us-east-1
```
*(Alternatively, you can run `aws dynamodb create-table` for each table with the respective Partition Keys).*

Once the script completes, go to **DynamoDB Console → Tables** to verify all 6 tables are created with **Active** status:

![DynamoDB Tables Active](/images/5-Workshop/student-portal/dynamodb-tables.png)

---
### Step 3: Create S3 Document Bucket & Configure CORS

The S3 Bucket is used to store actual files uploaded by students and teachers. Since the React frontend (running in the browser) will upload files directly to S3 via **Presigned URLs**, we must configure **CORS** (Cross-Origin Resource Sharing) to prevent the browser from blocking requests.

#### 1. Create S3 Bucket
Note: S3 bucket names must be **globally unique**. Replace `<yourname>` with your initials or a personal identifier:
```bash
aws s3 mb s3://student-documents-<yourname> --region us-east-1
```

#### 2. Apply CORS Configuration
Apply the `cors.json` configuration file provided in the repository:
```bash
aws s3api put-bucket-cors --bucket student-documents-<yourname> --cors-configuration file://cors.json
```
The `cors.json` file permits `PUT` and `POST` requests from any origin (`*`):
```json
{
  "CORSRules": [
    {
      "AllowedHeaders": ["*"],
      "AllowedMethods": ["GET", "PUT", "POST", "DELETE", "HEAD"],
      "AllowedOrigins": ["*"],
      "ExposeHeaders": ["ETag"]
    }
  ]
}
```

---
### Step 4: Create SQS Queue for Notifications

Create an SQS Queue to decouple email dispatch triggers from backend Lambda functions, forwarding them asynchronously to the Email Lambda Worker:
```bash
aws sqs create-queue --queue-name student-notifications --region us-east-1
```
Record the returned **Queue URL** (e.g., `https://sqs.us-east-1.amazonaws.com/123456789/student-notifications`).

---
### Step 5: Provision Amazon Cognito User Pool

Amazon Cognito manages user registration, login, and JWT access token issuance to secure API endpoints.

Run the automated Cognito setup script:
```bash
bash scripts/setup-cognito.sh us-east-1
```

The script performs the following actions:
1. Creates a Cognito User Pool named `student-portal-user-pool`.
2. Creates an App Client for React integration (without a client secret since it runs in a web browser).
3. Creates 3 User Groups (Cognito Groups): `Admin`, `Teacher`, and `Student`.
4. Creates a default administrator account:
   - **Username**: `admin@example.com`
   - **Temporary Password**: `Abc12345!` (you will be prompted to change this on your first login).

**Important Note**: Once the script completes, record the **UserPoolId** and **AppClientId** outputs to use in subsequent configuration steps.

![Cognito User Pool](/images/5-Workshop/student-portal/cognito.png)

---
### Step 6: Verify Email Address with Amazon SES

**Amazon SES (Simple Email Service)** is used to send automated email notifications (new account credentials, grade updates) to students and teachers.

> [!WARNING]
> By default, new AWS accounts start in **Sandbox** mode. In Sandbox mode, you can only send emails to **verified** addresses. Limits: **200 emails/24-hour period**, **1 email/second**.

#### 1. Verify Sender Email Identity
Go to **SES Console → Configuration → Identities → Create identity**, select **Email address**, and enter your sender email:
```bash
aws ses verify-email-identity --email-address your-sender@example.com --region us-east-1
```
Check your inbox and click the confirmation link from AWS.

#### 2. Verify Recipient Email (while in Sandbox)
Similarly, verify the recipient email (used for testing):
```bash
aws ses verify-email-identity --email-address recipient@example.com --region us-east-1
```

#### 3. Confirm SES Account Status
Go to **SES Console → Account dashboard** to verify:
- **Region**: US East (N. Virginia)
- **Status**: ✅ Healthy
- **Daily sending quota**: 200 emails/24-hour period
- **Maximum send rate**: 1 email per second

![Amazon SES Account Dashboard](/images/5-Workshop/student-portal/ses-dashboard.png)

> [!TIP]
> After completing the workshop, if you want to send real emails without restrictions, you can request AWS to **move out of Sandbox** by submitting a support request at SES Console → Account dashboard → **Request production access**.
