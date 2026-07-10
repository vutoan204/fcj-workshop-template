---
title: "Frontend Deployment with AWS Amplify"
date: 2024-01-01
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

# Deploy React Frontend via AWS Amplify (AWS Console)

In this section, you will deploy the React client application using **AWS Amplify**, hook up continuous deployment from GitHub, and configure the necessary client-side environment variables.

---

### Step 1: Connect GitHub Repository to AWS Amplify

1. Open the [AWS Amplify Console](https://console.aws.amazon.com/amplify/).
2. Click **Create new app** (or **Get Started** -> **Amplify Hosting**).
3. Select **GitHub** as the provider and click **Next** (authorize AWS Amplify to connect to your GitHub account if prompted).
4. **Repository:** Choose your `AWS-Project` GitHub repository.
5. **Branch:** Select the branch you want to deploy (e.g., `main`, `staging`, or your feature branch).
6. Click **Next**.

![AWS Amplify App Setup](/images/5-Workshop/5.6-Frontend-Amplify/amplify_app_setup.png)

---

### Step 2: Configure Build Settings & Environment Variables

AWS Amplify needs to know how to build the project and how to communicate with your backend APIs.

#### A. Configure Environment Variables
1. On the **App settings** page, expand the **Advanced settings** (or **Environment variables**) section.
2. Add the following key-value pairs (using the values you saved from Cognito and API Gateway steps):
   * `VITE_API_URL` = `https://<api-id>.execute-api.ap-southeast-1.amazonaws.com/dev` *(Ensure no trailing slash)*
   * `VITE_USER_POOL_ID` = `<your-user-pool-id>` (e.g., `ap-southeast-1_xxxxxx`)
   * `VITE_CLIENT_ID` = `<your-app-client-id>` (e.g., `7xxxxxxxxxxxxxxxxxxxxxxxxx`)
   * `VITE_AWS_REGION` = `ap-southeast-1`

![Amplify Environment Variables](/images/5-Workshop/5.6-Frontend-Amplify/amplify_env_setup.png)

#### B. Edit Build Specification (`amplify.yml`)
Because this project is configured as an npm workspaces monorepo, update the **Build command** and **Base directory** settings:
1. In the build settings configuration text area, paste the following custom `amplify.yml` config:
   ```yaml
   version: 1
   frontend:
     phases:
       preBuild:
         commands:
           - npm ci
       build:
         commands:
           - npm run --workspace=frontend build
     artifacts:
       baseDirectory: frontend/dist
       files:
         - '**/*'
     cache:
       paths:
         - node_modules/**/*
   ```
2. Click **Next**.

---

### Step 3: Review and Deploy

1. Review the repository details, branch name, environment variables, and build settings.
2. Click **Save and deploy**.
3. Amplify will begin provisioning, building, and deploying your frontend. This takes approximately 3-5 minutes.
4. Once completed, Amplify will output a **Domain URL** (e.g., `https://main.xxxxxxxx.amplifyapp.com`). Bounding this URL will open your live React application.
