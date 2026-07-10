---
title: "Resource Cleanup"
date: 2024-01-01
weight: 8
chapter: false
pre: " <b> 5.8. </b> "
---

# Resource Cleanup

To avoid incurring unexpected charges on your AWS account after completing the workshop, follow these steps to delete the created resources.

---

### Method A: Manual Deletion via AWS Management Console

Follow these steps to delete resources in reverse order to ensure all dependency barriers are cleared.

#### Step 1: Delete AWS Amplify Application
1. Open the [AWS Amplify Console](https://console.aws.amazon.com/amplify/).
2. Select your `smart-image-frontend` application.
3. Click **Actions** -> **Delete app**.
4. Type `delete` to confirm, then click **Delete**.

#### Step 2: Delete Amazon API Gateway
1. Open the [API Gateway Console](https://console.aws.amazon.com/apigateway/).
2. Locate `SmartImage-API` in your API list.
3. Click **Actions** -> **Delete**.
4. Confirm deletion.

#### Step 3: Delete AWS Lambda Functions
1. Open the [AWS Lambda Console](https://console.aws.amazon.com/lambda/).
2. Search for the following functions:
   * `SmartImage-ApiHandler`
   * `SmartImage-ImageProcessor`
   * `SmartImage-AIAnalyzer`
3. Select them, click **Actions** -> **Delete**.
4. Confirm deletion.

#### Step 4: Delete Amazon DynamoDB Tables
1. Open the [Amazon DynamoDB Console](https://console.aws.amazon.com/dynamodb/).
2. Click **Tables** from the left-hand menu.
3. Select `SmartImage-Images`, `SmartImage-UserQuotas`, and `SmartImage-UserProfiles`.
4. Click **Delete**.
5. Type `delete` to confirm.

#### Step 5: Delete Amazon S3 Buckets
*Note: S3 buckets must be completely empty before they can be deleted.*
1. Open the [Amazon S3 Console](https://s3.console.aws.amazon.com/).
2. Select your raw bucket `smartimage-raw-bucket-<your-name>`.
3. Click **Empty** and follow the instructions to confirm emptying.
4. Once emptied, click **Delete**, type the bucket name, and confirm.
5. Repeat the exact same steps to empty and delete your processed bucket `smartimage-processed-bucket-<your-name>`.

#### Step 6: Delete Amazon Cognito User Pool
1. Open the [Amazon Cognito Console](https://console.aws.amazon.com/cognito/).
2. Select `SmartImage-UserPool`.
3. Click **Delete**.
4. Confirm by entering the User Pool name.

#### Step 7: Delete IAM Roles & Policies
1. Open the [IAM Console](https://console.aws.amazon.com/iam/).
2. Click **Roles**, search for `SmartImage-` to find your Lambda execution roles, select them, and click **Delete**.
3. Click **Policies**, search for `SmartImage-`, select the custom policies, and click **Delete**.

#### Step 8: Delete CloudWatch Dashboards, Alarms & SNS Topics
1. Open the [Amazon CloudWatch Console](https://console.aws.amazon.com/cloudwatch/).
2. In the left panel, click **Dashboards**, select `SmartImage-dev-Operations`, and click **Delete**.
3. Click **Alarms** -> **All alarms**, select any alarm starting with `SmartImage-dev-`, and click **Delete**.
4. Open the [Amazon SNS Console](https://console.aws.amazon.com/sns/).
5. Click **Topics**, select `SmartImage-Alarms-dev`, and click **Delete**.

---

### Method B: Automated Deletion via Infrastructure-as-Code (AWS CDK)

If you deployed the stack using the AWS Cloud Development Kit (CDK):
1. **Empty S3 Buckets:** You must still empty your raw and processed S3 buckets manually on the console first (as S3 does not allow CloudFormation to delete non-empty buckets unless specifically configured with a force-delete policy).
2. **Execute CDK Destroy:** In your local development environment terminal, run the following command from the root of your CDK project:
   ```bash
   cdk destroy --all
   ```
3. Type `y` (yes) to confirm. This will automatically delete the API Gateway, Lambda functions, Cognito User Pools, IAM roles/policies, SQS DLQs, CloudWatch alarms, and operational dashboards in a single command.
