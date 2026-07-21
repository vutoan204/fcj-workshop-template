---
title: "Part I - Deploy with AWS CDK"
date: 2024-01-01
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

# Deploy Smart Image Platform with AWS CDK

This is the project's primary workflow. Configuration under `infrastructure/` is the source of truth whenever the Console reference differs.

## 1. Stack structure

| Stack | Main resources | Dependencies |
|---|---|---|
| Storage | Raw and processed buckets | None |
| Database | `Images`, `UserQuotas`, `UserProfiles` | None |
| Auth | Cognito User Pool, app client, groups | None |
| API | Lambda, SQS DLQs, API Gateway, S3/DynamoDB triggers, WAF | Storage, Database, Auth |
| Monitoring | Dashboard, alarms, SNS | API |
| Frontend | Amplify app and branch | API, Auth |

CDK adds the tags `Project=SmartImage`, `Environment=<environment>`, and `ManagedBy=CDK`. The default environment is `staging`, and the Region is fixed to `ap-southeast-1`.

## 2. Prepare environment variables

Set the repository, branch, and GitHub credential for the current terminal session. Do not store the token in source code:

```powershell
$env:GITHUB_REPO_URL = "https://github.com/<owner>/AWS-Project"
$env:GITHUB_BRANCH = "staging"
$env:GITHUB_TOKEN = "<token-from-secure-storage>"
```

When using an AWS CLI profile:

```powershell
$env:AWS_PROFILE = "<profile-name>"
aws sts get-caller-identity
```

## 3. Install dependencies and verify the project

Run from the `AWS-Project` root:

```bash
npm install
npm run build
npm test -- --runInBand
```

CDK uses `NodejsFunction` and esbuild to bundle TypeScript. `ImageProcessor` installs Sharp for Linux ARM64 inside the deployment artifact; do not upload raw TypeScript directories or Windows-installed `node_modules` directly to Lambda.

## 4. Bootstrap and review changes

Bootstrap is required once per account and Region:

```bash
npx cdk bootstrap aws://<account-id>/ap-southeast-1
npm run --workspace=infrastructure synth -- -c environment=staging
npm run --workspace=infrastructure cdk -- diff -c environment=staging
```

Review the template and diff before deployment, especially IAM changes, bucket replacements, and removal policies.

## 5. Deploy staging

```bash
npm run --workspace=infrastructure deploy:staging
```

The command deploys six stacks in dependency order. Record the CloudFormation outputs, including the API URL, User Pool ID, app client ID, bucket names, and Amplify URL.

## 6. Important CDK-created settings

- Raw bucket: private, SSE-S3, versioned, upload CORS, and lifecycle transitions.
- Processed bucket: private, SSE-S3, versioning disabled, GET/HEAD CORS.
- DynamoDB: On-demand, PITR, `NEW_AND_OLD_IMAGES` stream, and two GSIs on `Images`.
- Lambda: ARM64; `ApiHandler` 512 MB/15 seconds, `ImageProcessor` 1536 MB/120 seconds/1 GB `/tmp`, and `AiAnalyzer` 512 MB/60 seconds.
- S3 trigger: `OBJECT_CREATED` with the `users/` prefix.
- DynamoDB event source: batch size 10, `TRIM_HORIZON`, three retries, and an SQS on-failure destination.
- API Gateway: explicit resources/methods and a Cognito User Pool Authorizer.
- Amplify: the `staging` branch, four `VITE_*` variables, and an SPA rewrite to `/index.html`.

## 7. Current implementation notes

- The CDK source currently uses the Node.js 20.x Lambda runtime; upgrade to Node.js 22.x or another AWS-supported runtime before a new long-lived deployment.
- `cdk synth/list` currently reports deprecation warnings for `pointInTimeRecovery`, Cognito `advancedSecurityMode`, and Lambda `logRetention`. Migrate to `pointInTimeRecoverySpecification`, threat-protection properties, and `logGroup` in the next infrastructure update.
- CloudFront is disabled; the application returns S3 presigned GET URLs.
- A custom Authorizer Lambda is still created but is not used by API Gateway; the Cognito User Pool Authorizer is active.
- Production applies `RETAIN` to selected data resources; `cdk destroy` does not guarantee complete data removal or zero cost.
