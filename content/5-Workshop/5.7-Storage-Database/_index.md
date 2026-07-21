---
title: "Console - Storage and Database"
date: 2024-01-01
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

# Storage and Database in the AWS Console (optional)

> This section illustrates equivalent Console settings. Skip all resource-creation steps after deploying the `staging` CDK stacks.

## 1. Amazon S3

The system uses two private buckets to separate originals from processed output. For a manual exercise, use unique names with a `staging` suffix; physical names do not have to match CDK-generated names exactly.

### Raw bucket

1. Open the [Amazon S3 Console](https://s3.console.aws.amazon.com/) and verify that the Region is **Asia Pacific (Singapore) `ap-southeast-1`**.
2. Choose **Create bucket**.
3. Under **Bucket type**, select **General purpose**.
4. Enter a globally unique name such as `smartimage-staging-raw-images-<account-id>`. This is only for the Console path and need not match the CDK-generated physical name.
5. Keep **ACLs disabled (recommended)** under **Object Ownership**.
6. Under **Block Public Access settings**, keep **Block all public access** and all four child settings enabled.
7. Under **Bucket Versioning**, select **Enable**.
8. Under **Default encryption**, select **Server-side encryption with Amazon S3 managed keys (SSE-S3)**. Keep **Bucket Key** disabled; it applies to SSE-KMS.
9. Choose **Create bucket**.

![Initial settings for the staging raw bucket](/images/5-Workshop/5.3-Storage-Database/s3_raw_setup.png)

#### Configure the raw bucket after creation

1. Open the bucket and choose **Permissions** → **Cross-origin resource sharing (CORS)** → **Edit**.
2. Add a rule that allows frontend `PUT` and `POST` requests, required headers, exposes `ETag`, and uses max age `3600`. Use the Amplify origin; use `*` only temporarily before the frontend URL exists, then restrict it.
3. Choose **Management** → **Create lifecycle rule**.
4. Apply the rule to all objects in the bucket.
5. Transition current objects to **Standard-IA** after 90 days and **Glacier Flexible Retrieval** after 365 days.
6. Add a rule that aborts incomplete multipart uploads after **7 days** and a rule that deletes noncurrent versions after **30 days**. Review both deletion actions before using them with real data.

Minimal illustrative CORS configuration:

```json
[
  {
    "AllowedHeaders": ["*"],
    "AllowedMethods": ["PUT", "POST"],
    "AllowedOrigins": ["https://<amplify-branch-domain>"],
    "ExposeHeaders": ["ETag"]
  }
]
```

### Processed bucket

1. Return to the bucket list and choose **Create bucket**.
2. Enter a unique name such as `smartimage-staging-processed-images-<account-id>` in Region `ap-southeast-1`.
3. Keep **ACLs disabled** and **Block all public access**.
4. Under **Bucket Versioning**, select **Disable** to match the current CDK stack.
5. Select **SSE-S3** and keep **Bucket Key** disabled.
6. Choose **Create bucket**.
7. On the **Permissions** tab, add CORS for frontend `GET` and `HEAD` requests.
8. On the **Management** tab, create a lifecycle rule, select **Delete expired object delete markers or incomplete multipart uploads**, and abort incomplete multipart uploads after **7 days**. This bucket's CORS permits `GET`/`HEAD`, required headers, exposes `ETag`, and uses max age `3600`.

![Processed bucket versioning and encryption](/images/5-Workshop/5.3-Storage-Database/s3_processed_setup.png)

> Both buckets remain private. The frontend uses presigned URLs; do not enable static website hosting or a public-read policy.

## 2. Amazon DynamoDB

Create three tables in **On-demand** mode with default encryption and point-in-time recovery enabled:

| Example table | Partition key | Sort key | Purpose |
|---|---|---|---|
| `SmartImage-Images-staging` | `PK` (String) | `SK` (String) | Image metadata and related access patterns |
| `SmartImage-UserQuotas-staging` | `PK` (String) | `SK` (String) | Upload and storage quotas |
| `SmartImage-UserProfiles-staging` | `PK` (String) | `SK` (String) | User profiles |

![Primary key definition for the Images table](/images/5-Workshop/5.3-Storage-Database/dynamodb_table_setup.png)

### Create the Images table

1. Open the [Amazon DynamoDB Console](https://console.aws.amazon.com/dynamodb/) and choose **Tables** → **Create table**.
2. Enter `SmartImage-Images-staging` as the table name.
3. Enter `PK` as a **String** partition key.
4. Select **Add sort key** and enter `SK` as a **String**.
5. Select **Customize settings** and set capacity mode to **On-demand**.
6. Keep the default **Owned by Amazon DynamoDB** encryption.
7. Enable **Point-in-time recovery** if it is shown in the wizard; otherwise enable it after the table becomes **Active**.
8. Choose **Create table** and wait for **Active** before creating the stream or indexes.

### Images table stream

1. Open the `Images` table and choose **Exports and streams**.
2. Enable DynamoDB Streams with **New and old images** view type.
3. Do not create the Lambda trigger yet; it is illustrated in the Backend section.

### Create Global Secondary Indexes

1. Open `SmartImage-Images-staging` and choose the **Indexes** tab.
2. Choose **Create index**.
3. For the first GSI, enter `GSI1PK` as the partition key and `GSI1SK` as the sort key, both **String**.
4. Enter `GSI1-TagIndex-v2` as the index name.
5. Under **Attribute projections**, select **Include** and add `imageId`, `userId`, `thumbnailKey`, `originalFilename`, `createdAt`, `moderationStatus`, `imagePK`, and `imageSK`.
6. Choose **Create index** and wait for the index to become **Active**.
7. Choose **Create index** again; enter `GSI2PK`, `GSI2SK`, and the name `GSI2-ModerationIndex`.
8. Select **Include** and add `imageId`, `userId`, `originalKey`, `moderationLabels`, `thumbnailKey`, and `createdAt`.
9. Choose **Create index**.

The final configuration must match:

- `GSI1-TagIndex-v2`: String keys `GSI1PK` and `GSI1SK`; `INCLUDE` projection with `imageId`, `userId`, `thumbnailKey`, `originalFilename`, `createdAt`, `moderationStatus`, `imagePK`, and `imageSK`.
- `GSI2-ModerationIndex`: String keys `GSI2PK` and `GSI2SK`; `INCLUDE` projection with `imageId`, `userId`, `originalKey`, `moderationLabels`, `thumbnailKey`, and `createdAt`.

![GSI1 key schema](/images/5-Workshop/5.3-Storage-Database/dynamodb_gsi_setup.png)

> The screenshot illustrates only the GSI1 key schema. Projection `ALL` may be used for a shorter Console exercise, but it is optional and differs from CDK.

### Create the two supporting tables

Repeat **Tables** → **Create table** twice:

1. Create `SmartImage-UserQuotas-staging` with String keys `PK` and `SK`.
2. Create `SmartImage-UserProfiles-staging` with String keys `PK` and `SK`.
3. For both tables, select **On-demand**, default encryption, and point-in-time recovery.
4. Do not add streams or GSIs to these tables because the current CDK stack does not define them.

### Verify the result

- All three tables are **Active**.
- The Images table has two **Active** GSIs and a **New and old images** stream.
- Both buckets show **Block public access: On**.
- Versioning is enabled on the raw bucket and disabled on the processed bucket.
- No bucket policy grants public access. Lambda roles receive access in the Backend section.
- Allow HTTPS only. CDK uses `enforceSSL`; for a manual bucket, an equivalent bucket policy can deny requests where `aws:SecureTransport=false`.
