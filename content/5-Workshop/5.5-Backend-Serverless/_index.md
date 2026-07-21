---
title: "Console - Serverless Backend"
date: 2024-01-01
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

# Serverless Backend in the AWS Console (optional)

> This is a Console configuration map, not the primary deployment method. If the API stack exists, use the Console only for inspection; do not recreate functions, triggers, or APIs with the same names.

## 1. Execution roles and permissions

### Create the roles manually

Repeat this procedure for `ApiHandler`, `ImageProcessor`, and `AiAnalyzer`:

1. Open the [IAM Console](https://console.aws.amazon.com/iam/) → **Roles** → **Create role**.
2. Select **AWS service**, use case **Lambda**, and choose **Next**.
3. Attach the AWS managed policy `AWSLambdaBasicExecutionRole`.
4. Name the roles `SmartImage-ApiHandlerRole-staging`, `SmartImage-ImageProcessorRole-staging`, and `SmartImage-AiAnalyzerRole-staging`.
5. Create the role, open it, and choose **Add permissions** → **Create inline policy**.
6. Select **JSON**, add the actions in the table below, and replace example ARNs with the actual `staging` resources.
7. Choose **Next**, name the policy for the function, and choose **Create policy**.

Each Lambda has a separate execution role with `AWSLambdaBasicExecutionRole` and resource-scoped `staging` permissions:

| Lambda | Main permissions |
|---|---|
| `ApiHandler` | S3 Get/Put/Delete; DynamoDB Get/Put/Update/Delete/Query/Scan/BatchGet/BatchWrite on three tables and indexes; `cognito-idp:AdminUpdateUserAttributes` on the User Pool |
| `ImageProcessor` | S3 Get on the raw bucket, Put on the processed bucket; DynamoDB read/write/query on `Images`; SQS SendMessage for its DLQ |
| `AiAnalyzer` | S3 Get on the raw bucket; DynamoDB stream read and table read/write/batch; Rekognition DetectLabels/DetectModerationLabels; SQS SendMessage for its DLQ |

![Attach a custom policy to a Lambda role in the Console path](/images/5-Workshop/5.5-Backend-Serverless/iam_roles_setup.png)

> **Difference from CDK:** CDK creates roles and resource grants automatically. The custom managed policy in the screenshot is only a manual equivalent. Restrict resource ARNs to the actual buckets, tables, indexes, streams, queues, and User Pool.

In policy JSON, include both the table ARN and `table/<name>/index/*` for GSI queries. Use `table/<name>/stream/*` for streams and end S3 object ARNs with `/*`. Do not use `Resource: "*"` for S3, DynamoDB, or Cognito simply to make the lab work.

### Create two dead-letter queues

1. Open the [Amazon SQS Console](https://console.aws.amazon.com/sqs/) → **Create queue**.
2. Select **Standard**.
3. Create `SmartImage-ImageProcessorDlq-staging`, keep default encryption, and set message retention to 14 days.
4. Repeat for `SmartImage-AiAnalyzerDlq-staging`.
5. Record both queue ARNs for `sqs:SendMessage` permissions and on-failure destinations.

## 2. Lambda functions

The Console walkthrough focuses on three business functions:

| Function | Architecture | Memory | Timeout | Temporary storage |
|---|---:|---:|---:|---:|
| `SmartImage-ApiHandler-staging` | ARM64 | 512 MB | 15 seconds | Default |
| `SmartImage-ImageProcessor-staging` | ARM64 | 1536 MB | 120 seconds | 1024 MB |
| `SmartImage-AiAnalyzer-staging` | ARM64 | 512 MB | 60 seconds | Default |

The deployment screenshot shows Node.js 20.x because that is the runtime in the current CDK code. The current **Create function** screen offers Node.js 24.x; a manual function can use it after package compatibility is tested. Selecting a newer runtime in the Console does not update the CDK code.

![Lambda functions in staging after CDK deployment](/images/5-Workshop/5.5-Backend-Serverless/lambda_list.png)

Long names such as `CustomS3AutoDeleteObject`, `BucketNotificationsHandler`, and `LogRetention` are CDK provider functions. `SmartImage-Authorizer-staging` also exists, but the API uses the Cognito User Pool Authorizer.

### Create functions in the Console

For each business function:

1. Open the [AWS Lambda Console](https://console.aws.amazon.com/lambda/) → **Functions** → **Create function**.
2. Select **Author from scratch**.
3. Under **Basic information**, enter the exact **Function name** from the table above.
4. For **Runtime**, select **Node.js 24.x** or another supported Node.js runtime tested with the package.
5. Expand **Additional settings** under **Custom settings**.
6. Turn on **ARM64 architecture**.
7. Turn on **Custom execution role**. The **Configure custom execution role** panel opens on the right.
8. For **Execution role**, select **Choose an existing role**, select the matching role, and choose **Save** in the panel.
9. Leave **Durable execution**, **EC2 capacity provider**, Function URL, VPC, and other settings at their defaults because CDK does not use them.
10. Scroll to the bottom and choose **Create function**.

![Create a Lambda with ARM64 and a custom execution role in the current UI](/images/5-Workshop/5.5-Backend-Serverless/lambda_create_function.png)

11. After creation, open **Configuration** → **General configuration** → **Edit** and set memory and timeout from the table.
12. For ImageProcessor, set **Ephemeral storage** to `1024 MB`.
13. Open **Configuration** → **Environment variables** → **Edit** and enter the function values.

### Deployment package

Do not zip raw TypeScript source. Build with esbuild/`NodejsFunction`; `ImageProcessor` must include Sharp compiled for Linux ARM64. AWS SDK v3 can be externalized as in CDK or bundled deliberately.

To create a package equivalent to CDK:

1. Install dependencies and run the project build/tests before packaging.
2. Bundle each entry point with esbuild for the `node` platform, ARM64 architecture, and target runtime.
3. Verify that the configured handler is exported under the expected name, such as `index.handler`.
4. For ImageProcessor, install or bundle a `linux-arm64` Sharp binary; a Windows Sharp package will not run on Lambda Linux.
5. Zip the **contents** of the output directory, not an extra parent directory.
6. On the **Code** tab, choose **Upload from** → **.zip file**, upload the matching package, and choose **Save**.
7. For a package above the direct-upload limit, upload through S3 or use a layer/container image. That is an optional variation, not the current CDK configuration.

### Environment variables

All three functions use `IMAGE_TABLE_NAME`, `USER_QUOTA_TABLE_NAME`, `USER_PROFILE_TABLE_NAME`, `RAW_BUCKET_NAME`, `PROCESSED_BUCKET_NAME`, `ENVIRONMENT=staging`, `POWERTOOLS_SERVICE_NAME=SmartImage`, `POWERTOOLS_LOG_LEVEL=DEBUG`, and `NODE_OPTIONS=--enable-source-maps`.

Additional values:

- `ApiHandler`: `USER_POOL_ID`, `PRESIGNED_URL_EXPIRY=900`.
- `ImageProcessor`: `THUMBNAIL_WIDTH=200`, `THUMBNAIL_HEIGHT=200`, `RESIZED_MAX_WIDTH=1920`, `RESIZED_MAX_HEIGHT=1080`.
- `AiAnalyzer`: `RAW_BUCKET_NAME` and `IMAGE_TABLE_NAME` are required for image reads and metadata updates.

Use actual physical resource names rather than logical names. After saving, open **Configuration** → **Permissions** and verify the execution role. Run a small test event or inspect the log group to confirm that the function initializes before adding triggers.

![Actual environment variables for the staging ImageProcessor](/images/5-Workshop/5.5-Backend-Serverless/lambda_environment_variables.png)

The ImageProcessor example contains 15 variables. `AI_ANALYZER_FUNCTION_NAME` is empty in the current CDK deployment because DynamoDB Streams invokes AiAnalyzer; ImageProcessor does not invoke it directly.

## 3. S3 Event Notification

1. Open the raw bucket in the Amazon S3 Console.
2. Choose **Properties** and scroll to **Event notifications**.
3. Choose **Create event notification**.
4. Enter `InvokeImageProcessor-staging` as the name.
5. Enter prefix `users/` and leave suffix empty.
6. Under **Event types**, select **All object create events**.
7. Under **Destination**, select **Lambda function** and `SmartImage-ImageProcessor-staging`.
8. Choose **Save changes**. S3 adds the resource-based permission required to invoke Lambda.

The result must be:

- Event: **All object create events**.
- Prefix: `users/`.
- Destination: `SmartImage-ImageProcessor-staging`.
- Do not add a suffix when Lambda is responsible for format validation.

![S3 Event Notification destination](/images/5-Workshop/5.5-Backend-Serverless/s3_trigger_setup.png)

The screenshot illustrates the destination only; enter the event type and prefix listed above.

## 4. DynamoDB Stream event source

1. Verify that `SmartImage-Images-staging` has a **New and old images** stream.
2. Open the table → **Exports and streams** → **DynamoDB stream details**.
3. Choose **Create trigger**, or open `SmartImage-AiAnalyzer-staging` → **Add trigger** → **DynamoDB**.
4. Select the Images table stream ARN.
5. Set **Batch size** to `10` and enable the trigger.
6. Choose **Add/Create trigger**.

![Create a DynamoDB trigger with batch size 10](/images/5-Workshop/5.5-Backend-Serverless/dynamodb_trigger_setup.png)

After creation, open the event source mapping in Lambda and verify/configure:

- Starting position: `TRIM_HORIZON`.
- Maximum retry attempts: `3`.
- On-failure destination: `SmartImage-AiAnalyzerDlq-staging` SQS queue.
- Trigger enabled.

The screenshot shows only the initial trigger form; CDK also declares retry and destination settings.

If retry and destination are absent from the DynamoDB form, open Lambda → **Configuration** → **Triggers**, select the event source mapping, and choose **Edit**. Grant stream-read permission to the role before enabling the mapping, or the trigger will enter an error state.

## 5. API Gateway and Cognito Authorizer

### Create the REST API

1. Open the [API Gateway Console](https://console.aws.amazon.com/apigateway/) → **Create API**.
2. Under **REST API**, choose **Build**. Do not choose HTTP API because CDK uses REST API v1.
3. Select **New API**, enter `SmartImage-API-staging`, choose **Regional**, and choose **Create API**.

### Create the Cognito authorizer

1. In the new API, choose **Authorizers** → **Create authorizer**.
2. Enter `CognitoAuth` and select type **Cognito**.
3. Select Region `ap-southeast-1` and User Pool `SmartImage-UserPool-staging`.
4. Enter `Authorization` as the **Token source** and leave token validation empty.
5. Choose **Create authorizer**.

![Cognito User Pool Authorizer in API Gateway](/images/5-Workshop/5.5-Backend-Serverless/api_gateway_authorizer.png)

### Create resources and methods

1. Choose **Resources** → **Create resource** and begin with `/v1`.
2. Create the nested resource tree. Preserve braces in `{imageId}` so API Gateway treats it as a path parameter.
3. Select the target resource and choose **Create method**.
4. Under **Method details**, select the required **Method type**, such as `GET`, `POST`, `PATCH`, or `DELETE`.
5. For **Integration type**, select **Lambda function**.
6. Turn on **Lambda proxy integration** so the complete request is passed to ApiHandler as a structured event.
7. For **Response transfer mode**, select **Buffered**; this project does not use response streaming.
8. Under **Lambda function**, select Region `ap-southeast-1`, then find and select `SmartImage-ApiHandler-staging`.
9. Keep the default timeout unless another value is required. When saving, allow API Gateway to add invoke permission to the Lambda resource policy.
10. Choose **Create method**.

![Create a REST API method with Lambda proxy integration](/images/5-Workshop/5.5-Backend-Serverless/api_gateway_create_method.png)

11. Open the new method → **Method request** tab → **Edit**.
12. For a protected route, select **Authorization/Authorizer: CognitoAuth**. Keep **None** for `GET /v1/images/public`.
13. For `PATCH`/`POST`, select the request validator equivalent to CDK and save the method request.
14. Repeat according to this table:

| Method | Path | Authorization |
|---|---|---|
| `GET`, `PATCH` | `/v1/profile` | `CognitoAuth` |
| `POST` | `/v1/profile/avatar/presigned-url` | `CognitoAuth` |
| `GET` | `/v1/images` | `CognitoAuth` |
| `POST` | `/v1/images/presigned-url` | `CognitoAuth` |
| `GET` | `/v1/images/public` | `NONE` |
| `DELETE` | `/v1/images/bulk` | `CognitoAuth` |
| `GET` | `/v1/images/search` | `CognitoAuth` |
| `GET`, `DELETE`, `PATCH` | `/v1/images/{imageId}` | `CognitoAuth` |
| `GET` | `/v1/images/{imageId}/download` | `CognitoAuth` |
| `GET` | `/v1/admin/moderation` | `CognitoAuth` plus group check in Lambda |
| `POST` | `/v1/admin/moderation/{imageId}` | `CognitoAuth` plus group check in Lambda |

15. Enable CORS on resources called by the frontend; allow the Amplify origin, `Content-Type,Authorization` headers, and only the methods used.
16. Choose **Deploy API**, create stage `dev`, and deploy.
17. Copy the invoke URL: `https://<api-id>.execute-api.ap-southeast-1.amazonaws.com/dev`.
18. Call `GET /v1/images/public` without a token to verify the public route; a protected route without a token should return `401 Unauthorized`.

In the current CDK code, the `staging` environment maps to API Gateway stage `dev`; production uses `prod`.

> CDK currently also declares `/v1/auth/signup`, `/login`, and `/refresh`, but the Lambda router has no matching handlers; the frontend authenticates directly with Cognito. Do not use those three routes in the Console walkthrough.

## 6. AWS WAF Web ACL (optional Console path)

CDK creates a Regional AWS WAF v2 Web ACL and associates it with API Gateway stage `dev`. WAF evaluates requests before the Cognito authorizer, so a WAF-blocked request never reaches authentication or Lambda.

> AWS WAF has separate charges for Web ACLs, rules, and requests. Inspect the CDK-created resource instead of creating a manual duplicate when extra lab cost is not wanted.

### Create the Web ACL

The new Console labels a Web ACL as a **Protection pack (web ACL)**. This is a UI naming change; CDK and the API still use `WebACL`.

1. Open the [AWS WAF Console](https://console.aws.amazon.com/wafv2/homev2) → **Protection packs (web ACLs)**.
2. Check **Region scope**, select `ap-southeast-1`, and choose **Create protection pack (web ACL)**.

![Protection packs (web ACLs) in the new WAF interface](/images/5-Workshop/5.5-Backend-Serverless/waf_protection_packs.png)

3. Under **Tell us about your app**, select **API & integration services**. **Media & file processing** may also be selected, but it only affects Console recommendations and is not present in CDK.
4. Expand **Select resources to protect**, select **add resources** -> **Add regional resources**, API `SmartImage-API-staging`, and stage `dev`.
5. Under **Choose initial protections**, retain or add only the rules required to match CDK; other Console-recommended protection packages are optional.
6. Expand **Name and describe**, enter `SmartImage-ApiWebAcl-staging` and CloudWatch metric name `SmartImage-WafMetrics-staging`.
7. Keep the default body inspection size and default action **Allow**; CDK does not increase body inspection.
8. Continue to review and create the protection pack.

![Create protection pack wizard with app category and resource selection](/images/5-Workshop/5.5-Backend-Serverless/waf_create_protection_pack.png)

### Add AWS Managed Rules

1. Choose **Add rules** → **Add managed rule groups**.
2. Expand **AWS managed rule groups** and enable `Core rule set (AWSManagedRulesCommonRuleSet)`.
3. Keep rule-group actions (**Use rule actions/None**) to match CDK `overrideAction: none`.
4. Save it with priority `1` and enable CloudWatch metrics/sampled requests.
5. Outside the lab, the rule group can first run in Count mode to observe false positives. That operational choice differs from the current CDK configuration.

### Add the rate-based rule

1. Choose **Add rules** → **Add my own rules and rule groups** → **Rule builder**.
2. Select **Rate-based rule** and enter `RateLimitRule`.
3. Aggregate by **Source IP address**.
4. Enter rate limit `2000`, use the **5 minutes** evaluation window (the default when CDK omits the value), and choose **Block**.
5. Set priority `2`, metric name `RateLimitRuleMetric`, and enable CloudWatch metrics/sampled requests.
6. Keep the Web ACL default action **Allow** and choose **Create web ACL**.

### Verify association

1. Open API Gateway → `SmartImage-API-staging` → **Stages** → `dev` → **Edit**.
2. Under **Web application firewall (AWS WAF)**, select the new Web ACL and save.
3. Return to WAF → Web ACL → **Associated AWS resources** and verify that stage `dev` appears.
4. Inspect **Sampled requests** and both rule metrics after the API receives traffic.

<!-- Add WAF rules/association screenshot here later: /images/5-Workshop/5.5-Backend-Serverless/waf_rules_association.png -->

## 7. CloudWatch, alarms, and SNS (optional Console path)

### API Gateway access logs

1. Open CloudWatch → **Logs** → **Log management** → **Create log group**.
2. Enter `/aws/apigateway/SmartImage-staging`, select 30-day retention, and log class **Standard**.
3. Leave KMS key empty for service-managed encryption; deletion protection is optional and is not enabled by the current CDK stack.

![Create a CloudWatch log group with retention and log class](/images/5-Workshop/5.5-Backend-Serverless/cloudwatch_create_log_group.png)

4. Open API Gateway → API → **Stages** → `dev` → **Logs and tracing/Edit**.
5. Enable access logging, select the log-group ARN, and use a JSON format with standard fields such as request ID, IP, caller, HTTP method, resource path, status, protocol, response length, and request time.
6. CDK also enables X-Ray tracing for the stage. Turn on **X-Ray tracing** for an equivalent Console configuration.

### SNS topic and email subscription

1. Open the [Amazon SNS Console](https://console.aws.amazon.com/sns/) → **Topics** → **Create topic**.
2. Select **Standard**, name `SmartImage-Alarms-staging`, and display name `SmartImage staging Alarms`.
3. Keep Encryption, Access policy, and Delivery policy at their defaults for the lab, then choose **Create topic**.

![Create a Standard SNS topic for staging alarms](/images/5-Workshop/5.5-Backend-Serverless/sns_create_topic.png)

4. In the new topic, choose **Create subscription**, protocol **Email**, and enter the notification address.
5. Open the AWS Notification email and choose **Confirm subscription**. A `Pending confirmation` subscription receives no alarm notifications.

### Create alarms equivalent to CDK

1. In CloudWatch, choose **Alarms** → **All alarms** → **Create alarm**.
2. In Step 1 **Specify metric and conditions**, select data source **Metrics**, type **Classic**, and choose **Select metric**.

![Select Metrics and Classic when creating a CloudWatch alarm](/images/5-Workshop/5.5-Backend-Serverless/cloudwatch_create_alarm.png)

3. In the metric dialog, choose the Lambda, ApiGateway, or DynamoDB namespace and the dimension shown below.
4. Configure statistic, period, threshold, and missing-data handling.
5. In Step 2 **Configure actions**, select **In alarm** and send a notification to `SmartImage-Alarms-staging`.
6. In Step 3, use a name prefixed `SmartImage-staging-`; review Step 4 and choose **Create alarm**.

| Group | Metric and dimension | Statistic / Period | Condition |
|---|---|---|---|
| Each Lambda | `AWS/Lambda` → `Errors`, `FunctionName` | Sum / 5 minutes | `>5`, 1 period |
| Each Lambda | `AWS/Lambda` → `Duration`, `FunctionName` | p95 / 5 minutes | ApiHandler `>12000 ms`; ImageProcessor `>96000 ms`; AiAnalyzer `>48000 ms`, 2 periods |
| Each Lambda | `AWS/Lambda` → `Throttles`, `FunctionName` | Sum / 5 minutes | `>=1`, 1 period |
| API Gateway | `AWS/ApiGateway` → `5XXError`, `ApiName` | Sum / 5 minutes | `>10`, 1 period |
| API Gateway | `AWS/ApiGateway` → `Latency`, `ApiName` | p95 / 5 minutes | `>3000 ms`, 2 periods |
| Images DynamoDB | `AWS/DynamoDB` → `ThrottledRequests`, `TableName` | Sum / 5 minutes | `>=1`, 1 period |

Set missing data to **Treat missing data as not breaching**. Prefix alarm names with `SmartImage-staging-`; the Errors alarm requires at least six errors in one period because the comparison is **Greater than 5**.

### Create the operational dashboard

1. Choose **Dashboards** → **Create dashboard** and enter `SmartImage-staging-Operations`.
2. Add a text widget for the heading.
3. For each Lambda, add an **Invocations & Errors** graph: Invocations/Sum on the left axis and Errors/Sum on the right, period five minutes.
4. For each Lambda, add a **Duration (ms)** graph with Average and p95, period five minutes.
5. Arrange three graphs per row and choose **Save dashboard**.
6. Dashboards are global within an account, but their metric widgets still read a Region; verify that widgets use `ap-southeast-1`.

<!-- Add CloudWatch dashboard screenshot here later: /images/5-Workshop/5.5-Backend-Serverless/cloudwatch_dashboard_setup.png -->

The WAF and CloudWatch steps are Console illustrations. When CDK has already deployed the stacks, inspect and compare the existing resources instead of creating functional duplicates.
