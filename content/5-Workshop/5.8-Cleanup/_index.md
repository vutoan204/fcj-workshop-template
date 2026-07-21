---
title: "Resource Cleanup"
date: 2024-01-01
weight: 10
chapter: false
pre: " <b> 5.10. </b> "
---

# Resource Cleanup

Delete resources only from the intended account, Region, and environment. Do not destroy production before reviewing data-retention requirements.

## A. CDK-managed resources

From the `AWS-Project` root, verify the `staging` stack list:

```bash
npm run --workspace=infrastructure cdk -- list -c environment=staging
```

Then run:

```bash
npm run --workspace=infrastructure destroy -- -c environment=staging
```

1. Review the CDK stack list and verify that every name ends in `staging`.
2. Confirm when CDK prompts.
3. Follow CloudFormation Events. If a stack reaches `DELETE_FAILED`, inspect its first failed event to identify the dependency.

In `staging`, both S3 buckets use `autoDeleteObjects` and `DESTROY`, so CDK can empty and remove them during destruction. Manual emptying is unnecessary unless the deployment was changed or the custom resource fails.

After destruction, inspect CloudFormation and the relevant services to verify that all six stacks were deleted. Do not manually delete stack resources while destroy is still running.

## B. Resources created manually in the Console

`cdk destroy` does not remove resources created separately in the Console. Delete them in dependency-aware order.

### 1. Amplify and WAF

1. Open Amplify Console, select the `staging` app, choose **App settings** → **Delete app**, and complete the confirmation.
2. Open WAF Console and select the Web ACL in `ap-southeast-1`.
3. Remove its API Gateway stage association, then delete the Web ACL. Do not remove a Web ACL shared by another application.

### 2. API Gateway and triggers

1. Open API Gateway, select `SmartImage-API-staging`, verify the API ID, and choose **Delete**.
2. Open the raw S3 bucket → **Properties** → **Event notifications** and delete the ImageProcessor notification.
3. Open the AiAnalyzer Lambda → **Configuration** → **Triggers** and delete the DynamoDB event source mapping.
4. Verify that no enabled mapping remains before deleting the function.

### 3. Lambda, logs, and queues

1. In Lambda Console, delete the three manually created business functions. Delete long-name provider functions only when they are known to belong to this lab/stack.
2. In CloudWatch Logs, delete the three function log groups and `/aws/apigateway/SmartImage-staging` if audit logs do not need retention.
3. In SQS, delete `SmartImage-ImageProcessorDlq-staging` and `SmartImage-AiAnalyzerDlq-staging` after reviewing or exporting messages needed for analysis.

### 4. DynamoDB and S3

1. In DynamoDB, select each of the three `staging` tables.
2. Review backup/PITR requirements, create an on-demand backup if data must be retained, and choose **Delete table**.
3. For the processed bucket, choose **Empty**, complete the confirmation, and then **Delete bucket**.
4. For the versioned raw bucket, use **Empty** to remove current objects, object versions, and delete markers before deleting the bucket.
5. If Empty does not finish, enable **Show versions** and inspect remaining versions/delete markers.

### 5. Cognito, monitoring, and IAM

1. Open the correct `staging` User Pool in Cognito, ensure no other app uses it, and choose **Delete**. Its app client and groups are removed with the pool.
2. Delete CloudWatch dashboards and alarms prefixed `SmartImage-staging`.
3. Delete SNS subscriptions and then topic `SmartImage-Alarms-staging`.
4. Delete the three workshop IAM roles after Lambda no longer uses them; delete only inline/custom policies created for the lab.
5. Do not delete `AdministratorAccess`, service-linked roles, or policies used by other resources.

## C. Post-cleanup verification

- Recheck `ap-southeast-1` and any other Region used during the workshop.
- Search for resources with the `SmartImage` prefix/tag and `staging` environment.
- Inspect CloudFormation stacks in `DELETE_FAILED`.
- Check S3 object versions, CloudWatch Logs, SQS, SNS, WAF, and Amplify.
- Monitor Billing/Cost Explorer over the following days because cost data can be delayed.

Resource Explorer or Resource Groups Tag Editor can help search for `SmartImage`/`staging`, but also inspect S3, CloudWatch Logs, and retained resources directly because search results may not include every service immediately.

> Production applies `RETAIN` to selected buckets, tables, and the User Pool. `cdk destroy` does not remove retained resources. Do not claim zero cost until Billing confirms it and no out-of-stack resources remain.
