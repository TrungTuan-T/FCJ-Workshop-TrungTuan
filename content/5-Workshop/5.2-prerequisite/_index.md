---
title: "Prerequisites & Preparation"
date: 2026-07-10
weight: 2
chapter: false
---

To begin deploying the **AWS Student Management Portal** project, you need to prepare your development environment and configure AWS credentials on your computer.

---
### 1. Install Required Tools

#### 1.1. Node.js & npm (Runtime Environment)
Node.js is used to run Lambda backend code locally (for testing) and compile the React frontend.
- **Requirement**: Node.js `>= 18.x`, npm `>= 9.x`.
- **Installation**:
  - Visit the [Node.js Official Website](https://nodejs.org/) and download the latest LTS version.
  - During the Windows installation, ensure you check **"Add to PATH"**.
- **Verify**: Open Terminal/Command Prompt and run:
  ```bash
  node --version
  npm --version
  ```

#### 1.2. Python (For packaging scripts)
Python is used by automated scripts to compress the source code before deploying to AWS Lambda.
- **Requirement**: Python `>= 3.8`.
- **Installation**:
  - Download the installer from the [Python Official Downloads page](https://www.python.org/downloads/).
  - **IMPORTANT**: Check **"Add Python to PATH"** before clicking Install.
- **Verify**:
  ```bash
  python --version
  pip --version
  ```

#### 1.3. AWS CLI v2 (Command Line Interface)
AWS CLI allows you to interact with and create resources on AWS using automated scripts.
- **Installation**:
  - Download and run the MSI installer for Windows: [AWS CLI V2 MSI](https://awscli.amazonaws.com/AWSCLIV2.msi).
- **Verify**:
  ```bash
  aws --version
  ```

#### 1.4. Git Bash (Recommended for Windows Users)
The database initialization and deployment scripts in the project are written as Bash shell scripts (`.sh`). Therefore, if you are using Windows, it is highly recommended to install Git Bash.
- **Installation**: Download [Git for Windows](https://git-scm.com/download/win).
- Select **"Use Git and optional Unix tools from Windows Command Prompt"** during installation.

---
### 2. Configure AWS Credentials
To allow the AWS CLI to create resources in your AWS account, you must provide the Access Key of an IAM User with Administrator or specific resource creation privileges.

#### 2.1. Retrieve Access Key from AWS Console
1. Log in to the [AWS Management Console](https://console.aws.amazon.com/).
2. Search for the **IAM** service.
3. Choose **Users** from the left navigation → Click on your User.
4. Go to the **Security credentials** tab.
5. Scroll down to the **Access keys** section and click **Create access key**.
6. Select **Command Line Interface (CLI)**, complete the steps, and **download the CSV file containing your Access Key ID and Secret Access Key**.

#### 2.2. Configure AWS CLI on Your Machine
Open Terminal (or Git Bash) and run:
```bash
aws configure
```
Provide the requested details:
```text
AWS Access Key ID [None]: [Your Access Key ID]
AWS Secret Access Key [None]: [Your Secret Access Key]
Default region name [None]: us-east-1
Default output format [None]: json
```

> [!IMPORTANT]
> This project defaults to using the **us-east-1** (N. Virginia) region. Please ensure you configure it to `us-east-1` so all services are created in the same region, avoiding cross-region integration errors.

#### 2.3. Verify Configuration
Run the following command to check if AWS CLI is successfully authenticated to your account:
```bash
aws sts get-caller-identity
```
If you see a JSON response containing your `Account ID`, `Arn`, and `UserId`, your configuration is complete and you can move on to the next step!
