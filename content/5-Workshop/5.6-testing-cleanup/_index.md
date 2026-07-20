---
title: "Testing & Cleanup"
date: 2026-07-10
weight: 6
chapter: false
---

In this final section of the workshop, we will test the complete system APIs using the **Postman Collection**, monitor system logs using **CloudWatch**, and tear down the AWS services to prevent unexpected billing.

---
### 1. Test API Using Postman

The project includes a Postman Collection to help you rapidly test the backend REST APIs without needing to interact with the React Frontend.

#### 1.1. Prepare Postman
1. Open the **Postman** application.
2. Click **Import** → Choose the file `postman/student-management-api.postman_collection.json` from the project directory.
3. Configure the environment variables in Postman:
   - `baseUrl`: The Invoke URL of your API Gateway deployment (e.g., `https://xxxxxxxxxx.execute-api.us-east-1.amazonaws.com/prod`).
   - `idToken`: The JWT idToken string retrieved from Cognito upon login.

#### 1.2. Retrieve JWT Token from Cognito
After logging in with a registered account (e.g., `admin@example.com`), Cognito issues an `idToken` (JWT string).  
Copy this token and paste it into the Bearer Token section in Postman to authorize endpoints requiring administrative roles (such as creating students, deleting teachers, etc.).

---
### 2. Monitor Services via CloudWatch Logs

Whenever API Gateway or SQS triggers a Lambda execution, operational details are saved automatically inside **Amazon CloudWatch Logs**.

- **How to view logs**:
  1. Open the **CloudWatch Console** → Click **Log groups** on the left menu.
  2. Search for the log group associated with the Lambda function you want to debug (e.g., `/aws/lambda/getStudents` or `/aws/lambda/sendEmailWorker`).
  3. Select the latest log stream to view print output, trace details, or error messages.

---
### 3. AWS Resource Teardown (Cleanup)

> [!CAUTION]
> To prevent ongoing billing, please complete all the cleanup tasks below once you finish your evaluation or presentation.

Run the following commands in sequence in your Terminal or Command Prompt:

#### 1. Delete Lambda Functions
```bash
for fn in createStudent getStudents getStudentById updateStudent deleteStudent \
          createTeacher getTeachers getTeacherById updateTeacher deleteTeacher \
          createGrade getGrades getGradeById updateGrade deleteGrade \
          docUploadUrl docSaveMetadata materialUploadUrl materialSaveMetadata \
          getMaterials sendEmailWorker; do
  aws lambda delete-function --function-name $fn --region us-east-1
done
```

#### 2. Delete API Gateway
```bash
aws apigateway delete-rest-api --rest-api-id <API_ID> --region us-east-1
```

#### 3. Delete Cognito User Pool
```bash
aws cognito-idp delete-user-pool --user-pool-id <USER_POOL_ID> --region us-east-1
```

#### 4. Delete SQS Queue
```bash
aws sqs delete-queue --queue-url <QUEUE_URL> --region us-east-1
```

#### 5. Delete DynamoDB Tables
```bash
for table in Students Teachers Grades Materials Documents; do
  aws dynamodb delete-table --table-name $table --region us-east-1
done
```

#### 6. Delete S3 Buckets
Note: S3 buckets must be empty before deletion. Purge files first:
```bash
# Purge and delete document storage bucket
aws s3 rm s3://student-documents-<yourname> --recursive
aws s3 rb s3://student-documents-<yourname>

# Purge and delete frontend hosting bucket
aws s3 rm s3://student-portal-frontend-<yourname> --recursive
aws s3 rb s3://student-portal-frontend-<yourname>
```

#### 7. Delete CloudFront Distribution (If created)
1. Go to the CloudFront Console.
2. Select your distribution and click **Disable**.
3. Once the status shows disabled (takes a few minutes), select the distribution and click **Delete**.

#### 8. Delete IAM Role
```bash
aws iam delete-role --role-name student-portal-lambda
```

Congratulations on completing the Serverless Student Management Portal workshop on AWS!
