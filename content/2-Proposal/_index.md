---
title: "Proposal"
date: 2026-07-01
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Smart Image Platform  
## A Comprehensive AWS Serverless & Event-Driven Solution for Smart Image Storage and Processing  

### 1. Executive Summary  
**Smart Image Platform** is an advanced cloud computing solution designed to automate the upload, secure storage, compression, and AI-powered analysis of image content. The platform fully leverages **AWS Serverless** and **Event-Driven** architecture to ensure high availability, flexible scalability, and optimized operating costs. The entire frontend interface and API layer are strictly secured at the edge, with access restricted through Amazon Cognito for internal organizational accounts.

### 2. Problem Statement  
* **Current problem:** Traditional file management and processing systems often struggle to scale resources when the volume of uploaded files spikes. If image resizing, metadata extraction, and classification/tagging are processed synchronously on a traditional server, this causes bandwidth bottlenecks, consumes large amounts of hardware resources, and increases latency for end users.
* **Solution:** Build a fully asynchronous, event-driven architecture on AWS. Users interact through a Web interface hosted on **Amazon Amplify**, authenticated via Amazon Cognito. Every request is protected by an AWS WAF firewall sitting in front of Amazon API Gateway. Raw storage logic uses Amazon S3, which automatically triggers a chain of background Lambda functions: one branch handles image compression/resizing to optimize file size and pushes the result to a processed storage bucket, while another branch triggers Amazon Rekognition AI to automatically extract object tags and asynchronously records the results in Amazon DynamoDB. Finally, **Amazon CloudFront** sits in front of the storage layer to deliver processed image content back to users with low latency and high security.
* **Benefits and ROI:** Completely eliminates the cost of maintaining fixed server hardware running 24/7, thanks to the Pay-as-you-go model (paying only for actual code execution time in milliseconds and actual storage used). Saves up to 80% of infrastructure costs in the early stage by staying entirely within the AWS Free Tier. The system fully automates the AI-based image classification process, eliminating 100% of manual effort and improving the accuracy of stored metadata.

### 3. Solution Architecture  
The platform uses tightly coordinated Serverless services following a sequential data flow, as illustrated in the execution flow diagram below:

![Smart Image Platform Architecture](static/images/2-Proposal/architecture.jpeg)

* **Execution Flow Details:**
  1. **Users** access the frontend application interface hosted on **Amazon Amplify**.
  2. Amazon Amplify sends an authentication/login request to **Amazon Cognito**. After a successful login, the user receives a valid ID Token.
  3. Amazon Amplify sends the request with the Token through the **AWS WAF** firewall guarding the edge layer, while **Amazon Cognito** simultaneously performs authentication with **Amazon API Gateway** (step **3.1**) via the Cognito Authorizer to verify access rights before the request is forwarded.
  4. API Execution Layer:
     * **4.a:** API Gateway triggers **AWS Lambda (API Handler Lambda)** to process the logic and generate a secure S3 Presigned URL granting upload permission to the client.
     * **4.b:** In parallel, the API Handler Lambda writes an initial record of the request into **Amazon DynamoDB** to track the request's status.
  5. The client (Amazon Amplify) uses the Presigned URL to upload the image's binary data directly to the **S3 Bucket (Raw Store)**, minimizing load on the server.
  6. The successful file-write event in the **S3 Bucket** immediately triggers the **AWS Lambda (Image processor Lambda)** function to run asynchronously in the background.
  7. The Lambda function performs two parallel tasks:
     * **7.a:** Optimizes file size, compresses, resizes the image, and pushes the resulting file to the secure **processed S3 Bucket**.
     * **7.b:** Extracts basic metadata of the file (dimensions, format, creation date) and writes a record into **Amazon DynamoDB** (the data table in the AI Analysis subsystem).
  8. A new/updated metadata record event in DynamoDB (via DynamoDB Streams) triggers the **AWS Lambda (AI Analyzer Lambda)** for advanced analysis.
  9. The Lambda function calls the **Amazon Rekognition** AI service directly to detect objects, automatically generate tags, and perform content moderation, then writes these AI-generated labels back into the **DynamoDB** table.
 

### 4. Technical Implementation  
* **Implementation phases:**
  1. *Research and Architecture Design:* Analyze the image upload business flow, research how S3 Presigned URLs work, and design the event-driven architecture diagram (1 month).
  2. *Cost Estimation and Safety Margin Analysis:* Use the AWS Pricing Calculator to set spending limits and configure Budget Alerts to prevent the risk of runaway infinite loops (Month 1).
  3. *Development & Core Service Configuration:* Set up the network infrastructure, configure the DynamoDB tables, write the source code for the image-compression Lambda functions, and integrate the Rekognition AI API (Month 2).
  4. *Frontend Integration, Testing, and Deployment:* Connect the complete React App interface, perform stress tests on the concurrent image-processing flow, clean up unused resources, and deploy the system to the actual Production environment (Month 3).

* **Technical requirements:**
  * *Frontend:* A Single Page Application (React.js/Next.js) using the AWS Amplify SDK or Amazon Cognito Auth SDK to manage user login sessions. Integrates the Axios library to support direct binary-stream uploads to S3 via Presigned URLs.
  * *Backend & Cloud Infrastructure:* Lambda source code written in Python (using the Pillow/Boto3 libraries) or Node.js to optimize cold-start time. DynamoDB is configured in On-Demand mode for flexible storage. An Origin Access Control (OAC) policy is set up to protect the S3 Bucket from any unauthenticated public access.

### 5. Roadmap & Milestones  
* **Month 1:** Complete theoretical research on AWS infrastructure; register a Free Tier account and configure basic security (IAM, Budgets).
* **Month 2:** Finalize the system architecture design, successfully configure API Gateway and the DynamoDB tables, and complete the source code for the background image-processing Lambda chain.
* **Month 3:** Build out the frontend user interface, perform end-to-end integration testing from client to cloud, write technical documentation on GitHub, and complete project acceptance.

### 6. Budget Estimate  
The estimated cost is based on an MVP-scale system handling an average of under 5,000 images/month (staying entirely within the AWS perpetual Free Tier and the first 12 months):

* **AWS Lambda:** $0.00/month (within the free 1 million requests limit).
* **Amazon S3:** ~$0.05/month (storing raw and compressed images, under the free 5 GB limit).
* **Amazon DynamoDB:** $0.00/month (On-Demand mode, under the free 25 GB of static data storage).
* **Amazon API Gateway:** $0.00/month (under the free 1 million requests limit).
* **Amazon Rekognition:** $0.00/month (under the AWS Free Tier's free limit of 5,000 images/month).
* **AWS WAF:** ~$5.00/month (fixed cost for the Web ACL and basic rules — can be disabled during lab testing to optimize budget).

*Total estimated cost:* ~$0.00/month (if operating entirely within the Free Tier and disabling AWS WAF during internal testing), or ~$5.05/month (if the full WAF security shield is enabled).

### 7. Risk Assessment  
* **Infinite loop risk:** The image-processing Lambda overwrites the same folder after processing, repeatedly re-triggering itself and consuming a large amount of credits.
  * *Mitigation:* Fully separate two independent S3 buckets: `raw-images-bucket` (only this bucket is permitted to trigger the Lambda) and `processed-images-bucket` (used only to store the resulting images, with no trigger configured).
* **Denial-of-Service (DDoS) or API spam risk:** External users continuously send simulated requests, flooding the system with millions of Lambda executions.
  * *Mitigation:* An AWS WAF shield always sits in front of the system to block suspicious IPs, alongside Rate Limiting configuration (limiting the number of requests per minute per API key) at API Gateway.
* **Public S3 data leak risk:** Users' image files are accessed without authorization.
  * *Mitigation:* Enable the S3 bucket's Block Public Access feature, allowing file reads only through CloudFront URLs combined with secure signed authentication.

### 8. Expected Outcomes  
* **Technical outcomes:** The system achieves minimal response latency and a smooth interface, since heavy workloads (image compression, calling the AI tagging service) are fully offloaded to an asynchronous background processing layer running in real time.
* **Long-term value:** Successfully build a Clean Code-standard cloud software architecture framework capable of automatically scaling (Auto-scaling) from a handful of initial users up to tens of thousands of concurrent users, with no need to reconfigure the underlying hardware management system.
