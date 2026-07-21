---
title: "Workshop Overview"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

# Smart Image Platform Overview

Smart Image Platform is a serverless application for account registration, direct image upload to S3 through presigned URLs, thumbnail/resized-image generation, and content analysis with Amazon Rekognition.

## Deployed architecture

- **Amazon Cognito:** a User Pool, an SPA app client, and the `admin`/`user` groups.
- **Amazon S3:** private raw and processed buckets encrypted with SSE-S3.
- **Amazon DynamoDB:** three tables: `Images`, `UserQuotas`, and `UserProfiles`. The single-table pattern applies only within `Images`.
- **AWS Lambda:** `ApiHandler`, `ImageProcessor`, and `AiAnalyzer`; CDK currently also creates a custom `Authorizer`, although the API uses a Cognito User Pool Authorizer.
- **Amazon API Gateway:** a REST API with explicit resources and methods. `/v1/images/public` is public; other business routes use the Cognito authorizer where configured.
- **Amazon Rekognition:** label detection and content moderation.
- **Amazon SQS:** DLQs for `ImageProcessor` and the `AiAnalyzer` DynamoDB event source.
- **Amazon CloudWatch and SNS:** logs, metrics, an operations dashboard, and alarms.
- **AWS WAF:** protection for API Gateway.
- **AWS Amplify Hosting:** builds and deploys the React client from the environment's GitHub branch.
- **S3 presigned GET URLs:** current image delivery mechanism. CloudFront is disabled in the Storage stack.

## Two workshop paths

### Part I – AWS CDK

This is the primary deployment path. Six stacks are created for `staging` or `production`: Storage, Database, Auth, API, Monitoring, and Frontend.

### Part II – AWS Console (optional)

The Console section shows where equivalent settings appear in the AWS UI. Some screens do not expose every property declared by CDK; each relevant difference is called out beside the step.

> **Screenshot convention:** Screenshots use `staging` resources. Names, IDs, and generated suffixes vary by account and deployment, so compare resource types and settings rather than copying names verbatim.
