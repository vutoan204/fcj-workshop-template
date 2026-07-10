---
title: "Serverless Backend & Triggers"
date: 2024-01-01
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---


# Serverless Backend & Triggers (AWS Console)

In this section, you will create the IAM policies and roles first, deploy the Lambda functions, establish the triggers, and configure the Amazon API Gateway.

---

### Step 1: Create Custom IAM Policies

Before creating the execution roles for our Lambdas, we will create the custom permission policies under the IAM Access Management.

#### A. Create the API Handler Policy (`SmartImage-ApiHandler-Policy`)
1. Open the [IAM Console](https://console.aws.amazon.com/iam/).
2. In the left navigation pane, under **Access Management**, click **Policies**.
3. Click the orange **Create policy** button.
4. Under **Policy editor**, select the **JSON** tab (next to Visual). Delete the default code and paste the following policy:
   ```json
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Action": [
           "dynamodb:GetItem",
           "dynamodb:PutItem",
           "dynamodb:UpdateItem",
           "dynamodb:DeleteItem",
           "dynamodb:Query",
           "dynamodb:Scan"
         ],
         "Resource": [
           "arn:aws:dynamodb:ap-southeast-1:*:table/SmartImage-Images*",
           "arn:aws:dynamodb:ap-southeast-1:*:table/SmartImage-UserQuotas*",
           "arn:aws:dynamodb:ap-southeast-1:*:table/SmartImage-UserProfiles*"
         ]
       },
       {
         "Effect": "Allow",
         "Action": [
           "s3:PutObject",
           "s3:GetObject"
         ],
         "Resource": [
           "arn:aws:s3:::smartimage-raw-bucket-*/*",
           "arn:aws:s3:::smartimage-processed-bucket-*/*"
         ]
       },
       {
         "Effect": "Allow",
         "Action": [
           "cognito-idp:AdminUpdateUserAttributes"
         ],
         "Resource": "arn:aws:cognito-idp:ap-southeast-1:*:userpool/*"
       },
       {
         "Effect": "Allow",
         "Action": [
           "logs:CreateLogGroup",
           "logs:CreateLogStream",
           "logs:PutLogEvents"
         ],
         "Resource": "arn:aws:logs:*:*:*"
       }
     ]
   }
   ```
5. Click **Next**.
6. **Policy name:** Enter `SmartImage-ApiHandler-Policy`.
7. Click **Create policy**.

#### B. Create the Image Processor Policy (`SmartImage-ImageProcessor-Policy`)
1. Click **Policies** in the left menu, then click **Create policy**.
2. Select the **JSON** editor, paste the following policy:
   ```json
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Action": [
           "s3:GetObject"
         ],
         "Resource": "arn:aws:s3:::smartimage-raw-bucket-*/*"
       },
       {
         "Effect": "Allow",
         "Action": [
           "s3:PutObject"
         ],
         "Resource": "arn:aws:s3:::smartimage-processed-bucket-*/*"
       },
       {
         "Effect": "Allow",
         "Action": [
           "dynamodb:UpdateItem",
           "dynamodb:GetItem"
         ],
         "Resource": "arn:aws:dynamodb:ap-southeast-1:*:table/SmartImage-Images*"
       },
       {
         "Effect": "Allow",
         "Action": [
           "logs:CreateLogGroup",
           "logs:CreateLogStream",
           "logs:PutLogEvents"
         ],
         "Resource": "arn:aws:logs:*:*:*"
       }
     ]
   }
   ```
3. Click **Next**, name the policy `SmartImage-ImageProcessor-Policy`, and click **Create policy**.

#### C. Create the AI Analyzer Policy (`SmartImage-AIAnalyzer-Policy`)
1. Click **Policies** in the left menu, then click **Create policy**.
2. Select the **JSON** editor, paste the following policy:
   ```json
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Action": [
           "dynamodb:GetRecords",
           "dynamodb:GetShardIterator",
           "dynamodb:DescribeStream",
           "dynamodb:ListStreams"
         ],
         "Resource": "arn:aws:dynamodb:ap-southeast-1:*:table/SmartImage-Images*/stream/*"
       },
       {
         "Effect": "Allow",
         "Action": [
           "rekognition:DetectLabels",
           "rekognition:DetectModerationLabels"
         ],
         "Resource": "*"
       },
       {
         "Effect": "Allow",
         "Action": [
           "s3:GetObject"
         ],
         "Resource": "arn:aws:s3:::smartimage-raw-bucket-*/*"
       },
       {
         "Effect": "Allow",
         "Action": [
           "dynamodb:UpdateItem"
         ],
         "Resource": "arn:aws:dynamodb:ap-southeast-1:*:table/SmartImage-Images*"
       },
       {
         "Effect": "Allow",
         "Action": [
           "logs:CreateLogGroup",
           "logs:CreateLogStream",
           "logs:PutLogEvents"
         ],
         "Resource": "arn:aws:logs:*:*:*"
       }
     ]
   }
   ```
3. Click **Next**, name the policy `SmartImage-AIAnalyzer-Policy`, and click **Create policy**.

---

### Step 2: Create IAM Roles for Lambda Functions

Now we will create the execution roles and attach the custom policies created in Step 1.

#### A. Create `SmartImage-ApiHandler-Role`
1. Under **Access Management** in the left navigation pane, click **Roles**.
2. Click **Create role**.
3. **Step 1: Select trusted entity:** Select **AWS service** and choose **Lambda** as the use case. Click **Next**.
4. **Step 2: Add permissions page:**
   * In the search box, search for `SmartImage-ApiHandler-Policy`.
   * Check the checkbox next to it in the policy list. Click **Next**.
5. **Step 3: Name, review, and create page:**
   * **Role name:** Enter `SmartImage-ApiHandler-Role`.
   * Click **Create role**.

#### B. Create `SmartImage-ImageProcessor-Role`
1. Click **Roles** -> **Create role**. Select **Lambda** and click **Next**.
2. Search for and check the checkbox next to `SmartImage-ImageProcessor-Policy`. Click **Next**.
3. **Role name:** Enter `SmartImage-ImageProcessor-Role`. Click **Create role**.

#### C. Create `SmartImage-AIAnalyzer-Role`
1. Click **Roles** -> **Create role**. Select **Lambda** and click **Next**.
2. Search for and check the checkbox next to `SmartImage-AIAnalyzer-Policy`. Click **Next**.
3. **Role name:** Enter `SmartImage-AIAnalyzer-Role`. Click **Create role**.

![IAM Roles Setup](/images/5-Workshop/5.5-Backend-Serverless/iam_roles_setup.png)

---

### Step 3: Create and Deploy Lambda Functions

1. Open the [AWS Lambda Console](https://console.aws.amazon.com/lambda/).
2. Click **Create function**.
3. In the creation wizard:
   * Select **Author from scratch**.
   * **Runtime:** Select `Node.js 20.x` (or another Node version compatible with the backend).
   * Scroll down to **Custom settings** -> click to expand **Additional settings**.
   * Under the **General** section inside Additional settings:
     * Toggle **ARM64 architecture** to **On** (enables Graviton2 for better cost performance).
     * Toggle **Custom execution role** to **On** (to attach our pre-created IAM role).

#### A. Create `SmartImage-ApiHandler`
* **Function name:** Enter `SmartImage-ApiHandler`.
* **Custom execution role:** Select `SmartImage-ApiHandler-Role` in the dropdown.
* Click **Create function**.
* **Code & Deployment:** Zip the compiled files from `backend/api-handler/` and upload them under the **Code** tab.
* **Environment variables:** Under *Configuration -> Environment variables*, add:
   * `IMAGE_TABLE_NAME` = `SmartImage-Images`
   * `USER_QUOTA_TABLE_NAME` = `SmartImage-UserQuotas`
   * `USER_PROFILE_TABLE_NAME` = `SmartImage-UserProfiles`
   * `RAW_BUCKET_NAME` = `smartimage-raw-bucket-<your-name>`
   * `PROCESSED_BUCKET_NAME` = `smartimage-processed-bucket-<your-name>`
   * `USER_POOL_ID` = `<your-cognito-user-pool-id>`

#### B. Create `SmartImage-ImageProcessor`
* **Function name:** Enter `SmartImage-ImageProcessor`.
* **Custom execution role:** Select `SmartImage-ImageProcessor-Role` in the dropdown.
* Click **Create function**.
* **Code & Deployment:** Zip the compiled files from `backend/image-processor/` (containing the `Sharp` dependency compiled for Linux/Lambda runtime) and upload them.
* **Environment variables:**
   * `IMAGE_TABLE_NAME` = `SmartImage-Images`
   * `PROCESSED_BUCKET_NAME` = `smartimage-processed-bucket-<your-name>`

#### C. Create `SmartImage-AIAnalyzer`
* **Function name:** Enter `SmartImage-AIAnalyzer`.
* **Custom execution role:** Select `SmartImage-AIAnalyzer-Role` in the dropdown.
* Click **Create function**.
* **Code & Deployment:** Zip the compiled files from `backend/ai-analyzer/` and upload them.
* **Environment variables:**
   * `IMAGE_TABLE_NAME` = `SmartImage-Images`

![Lambda Functions List](/images/5-Workshop/5.5-Backend-Serverless/lambda_list.png)

---

### Step 4: Establish Triggers

#### A. Configure S3 Event Notification (Trigger ImageProcessor)
1. Go back to your S3 S3 bucket `smartimage-raw-bucket-<your-name>` in the S3 Console.
2. Select the **Properties** tab.
3. Scroll to **Event notifications** and click **Create event notification**.
4. **Event name:** Enter `TriggerImageProcessor`.
5. **Prefix:** Enter `users/` (to ensure the Lambda is only triggered when users upload images inside their dedicated directories).
6. **Event types:** Check **All object create events** (`s3:ObjectCreated:*`).
7. **Destination:** Select **Lambda function** and choose `SmartImage-ImageProcessor`.
8. Click **Save changes**.

![S3 Event Trigger Setup](/images/5-Workshop/5.5-Backend-Serverless/s3_trigger_setup.png)

#### B. Configure DynamoDB Stream Trigger (Trigger AIAnalyzer)
1. Go to the DynamoDB Console and click **Tables**.
2. Select `SmartImage-Images` and open the **Exports and streams** tab.
3. In **DynamoDB stream details**, click **Create trigger**.
4. **Lambda function:** Select `SmartImage-AIAnalyzer`.
5. **Batch size:** Enter `1` (for instant processing).
6. Keep other settings default and click **Create trigger**.

![DynamoDB Stream Trigger Setup](/images/5-Workshop/5.5-Backend-Serverless/dynamodb_trigger_setup.png)

---

### Step 5: Configure Amazon API Gateway

1. Open the [API Gateway Console](https://console.aws.amazon.com/apigateway/).
2. Click **Create API** -> Select **REST API** -> Click **Build**.
3. **API name:** Enter `SmartImage-API`.
4. Click **Create API**.
5. **Configure Cognito Authorizer:**
   * **Note:** To see the API-specific navigation menu (which contains "Authorizers"), you must first click on your API name (e.g., `SmartImage-API`) from the list of APIs.
   * In the new left-hand navigation pane that appears for your selected API, click **Authorizers** -> **Create authorizer**.
   * **Name:** Enter `CognitoAuth`.
   * **Type:** Select **Cognito**.
   * **Cognito User Pool:** Select `SmartImage-UserPool`.
   * **Token source:** Enter `Authorization` (in header).
   * Click **Create authorizer**.
 6. **Configure Resources & Proxy Method:**
   * In the left panel for your API, click **Resources**.
   * Select the root `/` (or `/v1` if you want to deploy under `/v1`) in the resource tree.
   * Click the **Create resource** button (located above the resource tree).
   * **Proxy resource:** Toggle to **On** (this will automatically configure it as a proxy resource `{proxy+}` and create the `ANY` method).
   * **CORS (Cross-Origin Resource Sharing):** Toggle to **On**.
   * Click **Create resource**.
   * Once created, select the **ANY** method under the newly created `{proxy+}` resource. (If it does not automatically open the method setup, click **Create method** under the resource).
   * In the **Method details / Integration** configuration screen:
     * **Integration type:** Select **Lambda function** (This is where the Integration type dropdown is located!).
     * **Lambda proxy integration:** Toggle to **On** (highly important!).
     * **Lambda function:** Select Singapore region and choose `SmartImage-ApiHandler`.
     * Click **Create method** (or **Save**).
 7. **Secure API Methods:**
   * Select the `ANY` method of the `/v1/images/{proxy+}` (or `{proxy+}`) resource in the tree.
   * Go to the **Method request** tab.
   * Click **Edit**.
   * **Authorization:** Select `CognitoAuth` in the dropdown.
   * Click **Save**.
 8. **Deploy API:**
   * Click the **Deploy API** button at the top right of the resources page.
   * **Stage:** Select **[New Stage]**, and enter the stage name as `dev`.
   * Click **Deploy**.

![API Gateway Authorizer](/images/5-Workshop/5.5-Backend-Serverless/api_gateway_authorizer.png)

   * *Note down the **Invoke URL** (e.g., `https://xxxx.execute-api.ap-southeast-1.amazonaws.com/dev`). You will need it to configure the React frontend.*
