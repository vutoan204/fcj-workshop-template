---
title: "Proposal"
date: 2026-07-01
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Smart Image Platform
## Image storage and processing with an AWS Serverless and Event-Driven architecture

### 1. Summary

**Smart Image Platform** supports image upload, storage, processing, and content analysis on AWS. It uses serverless and event-driven patterns to separate interactive requests from image-processing tasks that take longer to complete.

The React application is deployed with AWS Amplify Hosting. Amazon Cognito handles registration, email verification, sign-in, and authorization. API Gateway, Lambda, Amazon S3, DynamoDB Streams, and Amazon Rekognition form the main processing flow. AWS WAF protects the REST API, while CloudWatch, X-Ray, and Amazon SNS provide operational visibility and alerts.

The backend infrastructure is defined with AWS CDK so that configuration can be managed and deployed consistently across environments. The current implementation uses S3 presigned URLs for private image upload and retrieval. A dedicated CloudFront distribution for the processed bucket is not enabled.

### 2. Problem and Solution

* **Problem:** When resizing, metadata extraction, and AI analysis run synchronously on one server, response time can increase with image size and request volume. Operating that server also requires capacity planning, scaling, patching, and monitoring.
* **Solution:** Combine synchronous and asynchronous processing. Authentication, API calls, and presigned URL requests are synchronous. After an image is uploaded directly to the raw S3 bucket, resizing, thumbnail generation, metadata extraction, and Rekognition analysis run asynchronously through S3 Event Notifications, Lambda, and DynamoDB Streams.
* **Benefits:** Reduce the amount of image data passing through the backend API, move expensive processing out of the user's request path, and consume compute resources only when events occur. Classification and moderation are automated, but Rekognition results remain supporting data rather than a complete replacement for human review.

### 3. Solution Architecture

The following diagram shows the core processing flow. Supporting components such as the SQS dead-letter queue, CloudWatch, X-Ray, and SNS are omitted to keep it readable.

![Smart Image Platform Architecture](/images/2-Proposal/image.png)

* **Processing flow:**

  1. Users access the React application deployed with **AWS Amplify Hosting**.
  2. The application uses an **Amazon Cognito User Pool** for registration, email verification, and sign-in. After successful authentication, the client receives JSON Web Tokens for API requests.
  3. The client sends a request with the token to **Amazon API Gateway**. **AWS WAF** inspects the request before it reaches the API; API Gateway's Cognito Authorizer verifies the token before allowing the Lambda invocation.<br> 3.1. **Amazon API Gateway** passes the Token to **Amazon Cognito Authorizer** to validate the user's token and permissions before processing the request.
  4. 4.a. Upon successful verification, **Amazon API Gateway** invokes the **AWS Lambda (Presigned URL Generator)** function. <br> 4.b. **AWS Lambda (Presigned URL Generator)** initializes an initial data record in **DynamoDB**, generates a secure, time-limited S3 Presigned URL, and returns it to the Client via **API Gateway**.
  5. **Amazon Amplify** uses the received Presigned URL to upload the raw image file directly from the device to the **S3 Bucket** storage.
  6. The successful image upload event on **S3 Bucket** automatically triggers the **AWS Lambda (Image processor Lambda)** function.
  7. 7.a. **AWS Lambda (Image processor Lambda)** performs basic image processing tasks (such as compression, resizing) and saves the processed image into the **processed S3 Bucket**. <br> 7.b. Simultaneously, the **AWS Lambda (Image processor Lambda)** updates the image processing status in the **DynamoDB** database.
  8. Data modification events in **DynamoDB** trigger the **AWS Lambda (AI Analyzer Lambda)** function to initiate the advanced image analysis workflow.
  9. **AWS Lambda (AI Analyzer Lambda)** invokes the **Amazon Rekognition** API to extract labels, objects, and facial recognition data, then saves all analyzed data (Metadata) back into **DynamoDB**.
  
### 4. Technical Implementation

* **Implementation phases:**

  1. *Research and design:* Analyze the upload flow, S3 presigned URLs, event-driven processing, authentication, and data model.
  2. *Infrastructure:* Define CDK stacks for Storage, Database, Authentication, API, Monitoring, and Amplify Hosting; configure AWS Budgets for cost tracking.
  3. *Application development:* Build the frontend, API Handler, Image Processor, and AI Analyzer; integrate Rekognition, Cognito, and storage services.
  4. *Testing and deployment:* Run unit and end-to-end tests for registration, upload, image processing, and AI analysis; deploy staging and production environments. High-load capacity must be evaluated separately before publishing concurrent-user claims.

* **Implemented technology:**

  * *Frontend:* React 19 and Vite; `amazon-cognito-identity-js` for Cognito sessions; Zustand for state; native `fetch` for REST requests and presigned image transfers.
  * *Backend:* TypeScript on Node.js Lambda; AWS SDK for JavaScript v3; Sharp and `exif-reader` for image and metadata processing.
  * *Infrastructure:* AWS CDK in TypeScript; DynamoDB On-Demand with Point-in-Time Recovery; two private S3 buckets with Block Public Access; API Gateway REST API; Cognito User Pool; SQS dead-letter queue; WAF; CloudWatch; X-Ray; and SNS.
  * *Data protection:* The client receives a time-limited presigned URL for a specific operation. Lambda functions use IAM roles scoped to the required actions, and S3 public access is blocked.

### 5. Roadmap and Milestones

* **Phase 1:** Complete foundational study of AWS, IAM, Budgets, S3, Lambda, DynamoDB, API Gateway, Cognito, and related serverless patterns.
* **Phase 2:** Complete the architecture, CDK source code, and backend image-processing chain from S3 to Rekognition.
* **Phase 3:** Integrate the frontend, authentication, authorization, image library, and administration features; add WAF, logging, alarms, and SNS notifications.
* **Phase 4:** Run end-to-end tests, deploy staging and production, connect the domain, and complete the workshop documentation and demo video.

### 6. Budget Estimate

The deployment account was created after July 15, 2025 and uses the new AWS Free Tier program. It receives USD 100 in credits at registration and may earn up to an additional USD 100 by completing activities specified by AWS. The Free Plan ends after at most six months or when credits are depleted, whichever occurs first. Unused credits can continue to apply after an upgrade to the Paid Plan but expire 12 months after account creation.

The following estimate assumes `ap-southeast-1`, up to 5,000 new images per month, an average original size of 3 MB, and a combined 1 MB for the thumbnail and resized variants. Each image is analyzed once with DetectLabels and once with DetectModerationLabels. Frontend and API traffic remain at MVP scale, and no dedicated CloudFront distribution is enabled for the processed bucket.

| Component | Estimate after credits expire | Basis |
|---|---:|---|
| AWS WAF | USD 7–7.10/month | One Web ACL, one AWS Managed Rules group, one rate-based rule, and fewer than one million inspected requests |
| Amazon Rekognition | USD 1–2 at 1,000 images; USD 9–10 at 5,000 images | Two Group 2 APIs are called for every image; current free usage applies according to account eligibility |
| Amazon S3 | USD 0.50–1.50 in the first month | About 20 GB of new data at 5,000 images plus requests; accumulated storage grows over time and raw objects later transition to IA/Glacier |
| AWS Lambda | USD 0–1/month | Four functions; the Image Processor uses 1,536 MB and duration varies with image size |
| API Gateway and DynamoDB | USD 0–1/month | MVP traffic, three On-Demand tables, DynamoDB Streams, and PITR |
| CloudWatch, X-Ray, SNS, and SQS | USD 0.20–4/month | 14–30 day log retention, 12 standard alarms, one dashboard, traces, two DLQs, and email notifications |
| Amplify Hosting | USD 0–1/month | A small SPA with low build frequency and data transfer |
| Amazon Cognito | USD 0/month | MVP monthly active users remain below the applicable free allowance |

* **While Free Plan credits remain:** service costs are still recorded but offset by credits. The expected amount charged is approximately **USD 0/month**, provided the credits have not been depleted and traffic remains within the assumptions.
* **After credits expire:** approximately **USD 10–14/month** at up to 1,000 new images, or **USD 18–25/month** near 5,000 new images. These are planning ranges, not fixed prices.
* **Storage growth:** the raw bucket transitions to Standard-IA after 90 days and Glacier after 365 days, but the processed bucket has no expiration rule. Accumulated storage must be monitored separately.
* **Custom domain:** the domain and DNS are managed by a provider outside AWS. The project does not create a Route 53 hosted zone, so Route 53 and domain registration fees are excluded from this AWS estimate.

Actual charges must be verified with AWS Pricing Calculator, Billing, and Cost Explorer for the deployment account. The estimate excludes taxes, third-party domain fees, service-quota increases, and abnormal traffic.

References: [AWS Free Tier](https://aws.amazon.com/free/), [AWS WAF Pricing](https://aws.amazon.com/waf/pricing/), [Amazon Rekognition Pricing](https://aws.amazon.com/rekognition/pricing/), and [Amazon API Gateway Pricing](https://aws.amazon.com/api-gateway/pricing/).

### 7. Risk Assessment

* **S3 processing loop:** If Lambda writes its result to the same location that produces its trigger, the function can repeatedly invoke itself.
  * *Mitigation:* Use separate raw and processed buckets. Only the raw bucket sends S3 notifications to the Image Processor, and the trigger is limited to the `users/` prefix.
* **Asynchronous processing failure:** A Lambda event can fail because of an invalid file, timeout, or dependent service error.
  * *Mitigation:* Configure retries and an SQS dead-letter queue, with CloudWatch alarms and SNS error notifications.
* **API spam or abnormal traffic:** Repeated requests can increase API Gateway and Lambda invocations.
  * *Mitigation:* WAF applies the AWS Managed Rules Common Rule Set and a rate-based rule limited to 2,000 requests per source IP within the WAF evaluation window. The API Gateway stage has a rate limit of 1,000 requests per second and a burst limit of 500. These are stage-level settings, not per-API-key quotas.
* **S3 data exposure:** Objects might be accessed outside the authorized user's scope.
  * *Mitigation:* Enable Block Public Access, use least-privilege IAM, verify ownership in the API Handler, and issue only time-limited presigned GET and PUT URLs.
* **Inaccurate AI results:** Labels or moderation results can include false positives or false negatives.
  * *Mitigation:* Store confidence scores, use configurable thresholds, and allow administrator review when required.
* **Unexpected cost:** Log retention, PITR, WAF, accumulated S3 data, or repeated processing can increase the AWS bill and consume credits.
  * *Mitigation:* Configure AWS Budgets, suitable retention periods, CloudWatch alarms, and regular Cost Explorer reviews.

### 8. Expected Outcomes

* Users can register, verify email, sign in, manage profiles, and upload images through the web interface.
* Images remain in private buckets while the asynchronous flow creates thumbnail and resized variants, extracts metadata, detects labels, and performs content moderation.
* The API supports search, filters, pagination, a personal library, a community gallery, and Cognito-based administrative functions.
* Infrastructure can be deployed consistently with CDK for staging and production, with logs, traces, metrics, and operational alerts.
* Managed services can scale within their service quotas. Specific load capacity is reported only after the corresponding load tests and quota review have been completed.
