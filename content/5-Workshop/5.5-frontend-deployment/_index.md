---
title: "Frontend & Hosting Deployment"
date: 2026-07-10
weight: 5
chapter: false
---

In this section, we will explore the page structure of the **AWS Student Management Portal** frontend, configure the environment variables connecting to AWS Cognito and API Gateway, test the application locally, build the static bundle, host it on **Amazon S3**, and distribute it globally via **Amazon CloudFront**.

---
### 1. Frontend Structure & Role Permissions

The Frontend is built using **ReactJS** and packaged with the **Vite** build tool.

#### Frontend Code Directory Structure:
```text
frontend/
├── src/
│   ├── assets/
│   ├── components/
│   │   ├── ConfirmModal.jsx
│   │   ├── GradeForm.jsx
│   │   ├── Layout.jsx
│   │   ├── Navbar.jsx
│   │   ├── ProtectedRoute.jsx
│   │   ├── Sidebar.jsx
│   │   ├── StatusBadge.jsx
│   │   ├── StudentForm.jsx
│   │   └── TeacherForm.jsx
│   ├── config/
│   │   └── awsConfig.js
│   ├── pages/
│   │   ├── admin/
│   │   │   ├── AdminLogs.jsx
│   │   │   ├── AdminRoles.jsx
│   │   │   ├── AdminSettings.jsx
│   │   │   ├── AdminStudentList.jsx
│   │   │   ├── AdminTeacherList.jsx
│   │   │   ├── AdminUserCreate.jsx
│   │   │   ├── AdminUserDetail.jsx
│   │   │   ├── AdminUserEdit.jsx
│   │   │   └── AdminUsers.jsx
│   │   ├── auth/
│   │   │   ├── ForgotPassword.jsx
│   │   │   ├── NewPassword.jsx
│   │   │   ├── ResetPassword.jsx
│   │   │   └── VerifyCode.jsx
│   │   ├── common/
│   │   │   ├── ChangePassword.jsx
│   │   │   ├── Notifications.jsx
│   │   │   ├── Profile.jsx
│   │   │   └── ProfileEdit.jsx
│   │   ├── errors/
│   │   │   ├── Forbidden.jsx
│   │   │   └── NotFound.jsx
│   │   ├── grades/
│   │   │   ├── GradeCreate.jsx
│   │   │   ├── GradeDetail.jsx
│   │   │   ├── GradeEdit.jsx
│   │   │   ├── GradeList.jsx
│   │   │   └── TeacherGrades.jsx
│   │   ├── materials/
│   │   │   ├── MaterialDetail.jsx
│   │   │   ├── MaterialEdit.jsx
│   │   │   ├── StudentMaterials.jsx
│   │   │   └── UploadMaterial.jsx
│   │   ├── students/
│   │   │   ├── StudentCreate.jsx
│   │   │   ├── StudentDetail.jsx
│   │   │   ├── StudentDocuments.jsx
│   │   │   ├── StudentEdit.jsx
│   │   │   ├── StudentList.jsx
│   │   │   └── UploadDocument.jsx
│   │   ├── teachers/
│   │   │   ├── ClassDetail.jsx
│   │   │   ├── ClassList.jsx
│   │   │   ├── TeacherCreate.jsx
│   │   │   ├── TeacherDetail.jsx
│   │   │   ├── TeacherEdit.jsx
│   │   │   └── TeacherList.jsx
│   │   ├── Dashboard.jsx
│   │   ├── Login.jsx
│   │   └── Welcome.jsx
│   ├── services/
│   │   ├── adminService.js
│   │   ├── api.js
│   │   ├── authService.js
│   │   ├── classService.js
│   │   ├── documentService.js
│   │   ├── gradeService.js
│   │   ├── materialService.js
│   │   ├── studentService.js
│   │   └── teacherService.js
│   ├── styles/
│   ├── utils/
│   ├── App.jsx
│   ├── index.css
│   └── main.jsx
├── .env
├── package.json
└── vite.config.js
```

#### Authorization & Routing:
- **Protected Routing**: The client uses a `ProtectedRoute` component to intercept routing requests and check for valid Cognito JWT tokens.
- **Group-based Authorization**:
  - **Admin/Staff**: Full read and write permissions to the student, teacher, and grade modules, system logs, and settings.
  - **Teacher**: Authorized to view assigned classes, post/update student grades, and upload syllabus materials.
  - **Student**: Read-only access to personal profiles, grade sheets, and download course documents uploaded by teachers.

---
### 2. Configure Frontend Environment Variables

Create a `.env` file under the `frontend/` directory with the actual AWS service configuration below:

```env
# AWS Cognito Configuration
VITE_COGNITO_USER_POOL_ID=us-east-1_7SwNQ0qYm
VITE_COGNITO_CLIENT_ID=6o5g3hcus9ehbmk90acqeuplau

# API Gateway Endpoint (Deployed Invoke URL)
VITE_API_ENDPOINT=https://9k9i3ukwdh.execute-api.us-east-1.amazonaws.com/prod

# AWS Region
VITE_AWS_REGION=us-east-1

# App Configuration
VITE_APP_NAME=AWS Student Management Portal
VITE_APP_VERSION=1.0.0
VITE_ENABLE_NOTIFICATIONS=true
VITE_ENABLE_FILE_UPLOAD=true
```

> [!IMPORTANT]
> All React environment variables used via Vite **must** be prefixed with `VITE_`. Without this prefix, the app will not read the values and cannot connect to AWS services.

---
### 3. Local Development Run

With the `.env` file successfully configured, install dependencies and launch the Vite development server:

```bash
cd frontend
npm run dev
```
Open your browser and navigate to [http://localhost:3000](http://localhost:3000).

You can log in using the pre-seeded Admin account:
- **Email**: `admin@example.com`
- **Password**: `Abc12345!`

On your first login attempt, you will be prompted to replace the temporary password.

#### Login Screen (Login Page):
![Login Page](/images/5-Workshop/student-portal/login_page.png)

#### Admin Dashboard:
![Dashboard Page](/images/5-Workshop/student-portal/dashboard_page.png)

#### Student Management Interface:
![Students List Page](/images/5-Workshop/student-portal/students_list.png)

---
### 4. Build Production Bundle & Deploy to Amazon S3

When local checks are complete, compile the React assets into optimized static HTML, JS, and CSS files for hosting.

#### 1. Compile the Static Bundle (Build)
```bash
npm run build
```
This outputs all assets to a target directory named `dist/`.

#### 2. Create S3 Bucket for Hosting
Create a new S3 bucket to host the static assets (must be globally unique):
```bash
aws s3 mb s3://student-portal-frontend-<yourname> --region us-east-1
```

#### 3. Upload Assets (Deploy)
Synchronize the local `dist/` directory with your S3 bucket:
```bash
aws s3 sync dist/ s3://student-portal-frontend-<yourname> --delete
```

---
### 5. Distribute via Amazon CloudFront

To optimize global load speed and serve the app over HTTPS, create an **Amazon CloudFront Distribution** pointing to your S3 frontend bucket as the Origin:

1. Open **CloudFront Console** → Click **Create distribution**.
2. Set **Origin domain** to your S3 frontend bucket.
3. Configure **Origin access control (OAC)** to block direct S3 access.
4. Set **Default root object** to `index.html`.
5. Wait for the distribution to deploy (5–10 minutes).

Once deployed, the system is accessible via the CloudFront URL:

> 🌐 **Live Demo:** [https://d3th0yl82lu593.cloudfront.net](https://d3th0yl82lu593.cloudfront.net)

> [!TIP]
> To push updates after code changes, re-run `npm run build`, sync to S3, then invalidate the CloudFront cache:
> ```bash
> aws cloudfront create-invalidation --distribution-id <DISTRIBUTION_ID> --paths "/*"
> ```
