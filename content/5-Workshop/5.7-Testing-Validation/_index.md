---
title: "Testing and Verification"
date: 2024-01-01
weight: 9
chapter: false
pre: " <b> 5.9. </b> "
---

# Test Smart Image Platform

The following steps use the CDK `staging` deployment. Substitute equivalent names when resources were created manually.

## 1. User authentication

1. Open the Amplify branch URL or a custom domain pointing to Amplify.
2. Sign up with an email, full name, and a password satisfying the configured policy.
3. Open the Cognito email, enter the confirmation code, and complete registration.
4. For group testing, open Cognito Console → User Pool → **Users**, select the account, and add it to `user` or `admin`.
5. Sign in again after assigning the group so a new token contains `cognito:groups`.
6. In DevTools → Network, verify that protected requests include `Authorization`; do not expose the token in workshop screenshots.

![Screen displayed after sign-up confirmation](/images/5-Workshop/5.7-Testing-Validation/react_app_login.png)

## 2. Image upload and processing

1. Select a valid JPEG or PNG in Upload Images.
2. Choose **Upload** and verify that presigned URL generation succeeds.
3. Confirm that the browser PUTs the object directly to the raw bucket; API Gateway/Lambda does not receive the full image binary.
4. Open the raw bucket and verify the object under `users/<user-id>/`.
5. Follow the image status in the UI; processing is asynchronous, so poll until a terminal state is reached.
6. In the processed bucket, inspect `resized/` and `thumbnails/` under `users/<user-id>/`.
7. Compare original, resized, and thumbnail object sizes to verify that Sharp produced outputs rather than copying the file.

![Output folders in the staging processed bucket](/images/5-Workshop/5.7-Testing-Validation/s3_processed_objects.png)

## 3. Metadata and Rekognition

1. Open DynamoDB Console → **Tables** → `SmartImage-Images-staging`.
2. Choose **Explore table items** and locate the item for the uploaded user/image.
3. Open the item in view mode and inspect:

- `PK`/`SK` follow the access pattern.
- `status` reflects pipeline progress.
- `thumbnailKey`/`resizedKey` point to the processed bucket.
- `aiTags`, moderation labels/status, and EXIF are present after their respective steps complete.

![Inspect aiTags on a DynamoDB item](/images/5-Workshop/5.7-Testing-Validation/dynamodb_item_tags.png)

Use the screen for observation only; do not edit `aiTags` manually.

4. If image-processing metadata exists but AI labels do not, inspect the DynamoDB stream, AiAnalyzer event source mapping, and logs before attributing the problem to Rekognition.

## 4. Frontend gallery

1. Return to the frontend and open **My Gallery**.
2. Refresh or wait for the next poll, then select the processed image.
3. Inspect its preview, state, metadata, and AI labels.
4. Open/download the image; the returned value should be an expiring presigned GET URL, not a permanent public S3 URL.
5. Open **Community Gallery** and verify that `GET /v1/images/public` does not require a token.
6. Sign out and open My Gallery; the protected route should require authentication.

![A COMPLETED image with Rekognition labels](/images/5-Workshop/5.7-Testing-Validation/react_app_dashboard.png)

## 5. Logs, metrics, and alarms

1. Open CloudWatch Console → **Logs** → **Log groups**.
2. Open the latest stream for each Lambda, match its timestamp to the upload, and find the same request/image ID.
3. Inspect API access logs for method, path, and status code.
4. Open **Dashboards** → `SmartImage-staging-Operations` and select a time range covering the test.
5. Inspect:

- Lambda log groups corresponding to `SmartImage-ApiHandler-staging`, `SmartImage-ImageProcessor-staging`, and `SmartImage-AiAnalyzer-staging`.
- API access log group `/aws/apigateway/SmartImage-staging`.
- Dashboard `SmartImage-staging-Operations`.
- SNS topic `SmartImage-Alarms-staging`; confirm the email subscription before expecting notifications.
- SQS queues `SmartImage-ImageProcessorDlq-staging` and `SmartImage-AiAnalyzerDlq-staging`.

The Lambda Errors alarm uses a five-minute `Sum`, threshold 5, and **GreaterThanThreshold**; at least six errors in one period are required to enter ALARM.

A successful invocation does not prove that alarms or DLQs work. Both require an error that the handler does not swallow, and the alarm also requires enough failures to exceed its threshold.

## 6. Failure testing

### A. Frontend validation

1. Select an unsupported extension such as `.txt`.
2. Verify that the frontend rejects it before upload.
3. Confirm that the raw bucket has no new object. This does not create a Lambda error and cannot test the alarm.

### B. Backend validation

1. In `staging` only, upload an object with an image extension but corrupt content directly under `users/<test-user>/` in the raw bucket.
2. Open ImageProcessor logs and verify that validation/Sharp reports the failure.
3. Confirm that the test item is not shown as a valid `COMPLETED` image.

### C. Current test limitation

`ImageProcessor` and `AiAnalyzer` currently catch errors without rethrowing in some paths. When Lambda treats the invocation as successful, the `Errors` metric, retries, and DLQ do not behave like an unhandled failure. Report SNS/DLQ validation as successful only after correcting error propagation or running a separate controlled alarm test.

## 7. Record evidence

Record the timestamp, image ID, API status, raw/processed keys, DynamoDB state, and matching log stream. Distinguish **observed** results from **expected** results; do not report an alarm or DLQ as successful without Console evidence.

> Do not generate repeated failures in production. Remove test objects and items after validation.
