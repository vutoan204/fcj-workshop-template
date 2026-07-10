---
title: "Prerequisites"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

# Workshop Prerequisites

Before beginning the setup of the **Smart Image Platform**, ensure you have completed the following steps:

### 1. AWS Account
* You must have an active **AWS Account**.
* Your IAM user or role used to log in to the AWS Management Console must have administrative privileges (e.g., `AdministratorAccess` policy).
* Ensure you know your target AWS Region (the default region for this project is `ap-southeast-1` - Singapore).

![AWS Management Console Login](/images/5-Workshop/5.2-Prerequisites/IAM_Console.png)

### 2. Local Development Environment
Make sure the following tools are installed on your machine if you wish to build or deploy programmatically:
* **Node.js** (version 20 or higher)
* **npm** (version 10 or higher)
* **AWS CLI** (configured with credentials of your AWS Account using `aws configure`)

### 3. GitHub Repository (For Frontend CI/CD)
The client frontend application will be hosted on AWS Amplify, which uses continuous integration/continuous deployment (CI/CD) linked to GitHub.
* Push your project code (`AWS-Project`) to a private or public **GitHub Repository**.
* Make sure you have a **GitHub Personal Access Token (PAT)** or authorize AWS Amplify to access your GitHub account. If creating a PAT, ensure it has the `repo` and `admin:repo_hook` scopes enabled.

![GitHub Repository](/images/5-Workshop/5.2-Prerequisites/GitHub_Repo.png)
