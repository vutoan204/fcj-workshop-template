---
title: "Storage & Database Configuration"
date: 2024-01-01
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

# Storage & Database Configuration (AWS Console)

In this section, you will create the storage layers (Amazon S3 buckets) and database layer (Amazon DynamoDB) required for the Smart Image Platform.

---

### Step 1: Create Amazon S3 Buckets

The platform uses two separate S3 buckets to enforce security boundaries and prevent infinite Lambda loops:
1. **Raw Bucket:** Stores the original uploaded images.
2. **Processed Bucket:** Stores thumbnails and optimized images.

#### A. Create the Raw Images Bucket
1. Open the [Amazon S3 Console](https://s3.console.aws.amazon.com/).
2. Click **Create bucket**.
3. **Bucket name:** Enter a unique name, e.g., `smartimage-raw-bucket-<your-name>`.
4. **AWS Region:** Select `ap-southeast-1` (Singapore).
5. **Object Ownership:** Leave as **ACLs disabled (recommended)**.
6. **Block Public Access settings for this bucket:** Check **Block *all* public access** (highly important).
7. **Bucket Key:** Leave **Enable** selected under Encryption settings.
8. Click **Create bucket**.

![S3 Raw Bucket Setup](/images/5-Workshop/5.3-Storage-Database/s3_raw_setup.png)

#### B. Create the Processed Images Bucket
1. Click **Create bucket** again.
2. **Bucket name:** Enter a unique name, e.g., `smartimage-processed-bucket-<your-name>`.
3. **AWS Region:** Select `ap-southeast-1` (Singapore).
4. **Block Public Access settings for this bucket:** Check **Block *all* public access**.
5. Click **Create bucket**.

![S3 Processed Bucket Setup](/images/5-Workshop/5.3-Storage-Database/s3_processed_setup.png)

---

### Step 2: Create Amazon DynamoDB Tables

We will create three tables: `SmartImage-Images` (main metadata catalog), `SmartImage-UserQuotas` (upload volume tracking), and `SmartImage-UserProfiles` (user metadata settings).

#### A. Create the Main Images Table
1. Open the [Amazon DynamoDB Console](https://console.aws.amazon.com/dynamodb/).
2. Click **Create table**.
3. **Table name:** Enter `SmartImage-Images`.
4. **Partition key:** Enter `PK` (Data type: **String**).
5. **Sort key:** Check **Add sort key** and enter `SK` (Data type: **String**).
6. **Table settings:** Select **Customize settings**.
7. **Read/write capacity settings:** Select **On-demand** (to enable automatic scaling and fit within the Free Tier).
8. Keep all other settings default and click **Create table**.

![DynamoDB Table Setup](/images/5-Workshop/5.3-Storage-Database/dynamodb_table_setup.png)

#### B. Enable DynamoDB Streams
*Note: In the new AWS Console UI, DynamoDB streams are configured after the table is created.*
1. In the DynamoDB table list, select your newly created `SmartImage-Images` table.
2. Navigate to the **Exports and streams** tab.
3. Scroll down to the **DynamoDB stream details** section and click **Turn on** (or **Enable**).
4. **View type:** Select **New and old images** (crucial for triggering the AI Analyzer Lambda function later).
5. Click **Turn on stream**.

#### C. Configure Global Secondary Indexes (GSIs)
Configure the indexes in the table details page:
1. Under your `SmartImage-Images` table, navigate to the **Indexes** tab.
2. Click **Create index** (or **Create global index**).
3. **Partition key:** Enter `GSI1PK` (String).
4. **Sort key:** Check **Add sort key** and enter `GSI1SK` (String).
5. **Index name:** Enter `GSI1-TagIndex-v2`.
6. **Attribute projections:**
   * **Recommended option (matches CDK exactly):** Select **Include** and manually type (since the table is new and has no data, you must manually type and click **Add** for each) the following non-key attributes:
     * `imageId`, `userId`, `thumbnailKey`, `originalFilename`, `createdAt`, `moderationStatus`, `imagePK`, `imageSK`
   * **Alternative option (simpler):** Select **All** (Projects all attributes into the GSI. This is simpler to configure on the Console and avoids missing attributes, but consumes slightly more storage).
7. Click **Create index**.
8. Repeat the steps to create the second GSI:
   * **Partition key:** `GSI2PK` (String).
   * **Sort key:** `GSI2SK` (String).
   * **Index name:** `GSI2-ModerationIndex`.
   * **Attribute projections:** Select **Include** and add: `imageId`, `userId`, `originalKey`, `moderationLabels`, `thumbnailKey`, `createdAt` (or choose **All** for simplicity).
9. Click **Create index**.

![DynamoDB GSIs Setup](/images/5-Workshop/5.3-Storage-Database/dynamodb_gsi_setup.png)

#### D. Create User Quotas Table
1. Click **Tables** -> **Create table**.
2. **Table name:** Enter `SmartImage-UserQuotas`.
3. **Partition key:** Enter `PK` (String).
4. **Sort key:** Check **Add sort key** and enter `SK` (String).
5. **Table settings:** Select **Customize settings** and set **Read/write capacity settings** to **On-demand**.
6. Click **Create table**.

#### E. Create User Profile Table
1. Click **Create table**.
2. **Table name:** Enter `SmartImage-UserProfiles`.
3. **Partition key:** Enter `PK` (String).
4. **Sort key:** Check **Add sort key** and enter `SK` (String).
5. **Table settings:** Select **Customize settings** and set **Read/write capacity settings** to **On-demand**.
6. Click **Create table**.
