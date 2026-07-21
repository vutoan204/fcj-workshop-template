---
title: "Week 11 Worklog"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 1.11. </b> "
---



### Week 11 Objectives:
* Design tiered file storage partitioning (raw images vs. processed images buckets) and configure NoSQL data table structures on Amazon DynamoDB with secondary indexes.
* Validate the feasibility of an event-driven automated image processing workflow using S3 Event Notifications to trigger background Lambda functions.
* Implement DynamoDB Streams to capture real-time data modification events and trigger asynchronous AI analytical Lambda functions.
* Study Amazon Rekognition AI API documentation for object detection and label extraction, alongside CloudFront CDN distribution strategies.

### Tasks Completed This Week:
| Day | Work Content | Start Date | End Date | Reference / Resource |
| --- | --- | --- | --- | --- |
| 2 | - Researched solutions to partition Object Storage into 2 isolated zones (original image bucket and compressed image bucket) and configured CORS cross-access blocking. | 06/29/2026 | 06/29/2026 | <https://000057.awsstudygroup.com/vi/> |
| 3 | - Developed a NoSQL table configuration plan on DynamoDB, selecting the Partition Key and establishing secondary indexes to optimize image metadata search speeds. <br> Research key design strategies (Partition Key, Sort Key) and auto-scaling mechanisms. | 06/30/2026 | 06/30/2026 | <https://000060.awsstudygroup.com/vi/> |
| 4 | - Studied AWS documentation to apply the S3 Event Notification feature, designing an automated workflow to trigger Lambda functions as soon as a new file arrives in the original image bucket. <br> - Explore Amazon S3 object storage solutions and S3 Glacier storage classes for cold data archiving. <br> - Configure S3 Bucket Security Policies and establish Lifecycle Management Rules for automated data transition. | 07/01/2026 | 07/01/2026 | <https://000078.awsstudygroup.com/vi/> <https://000078.awsstudygroup.com/vi/2-resize-image-function/2-2-create-s3-bucket/> |
| 5 | - Conducted theoretical testing of the event-driven activation flow, reviewing the validity of sample data file structures passed between services. | 07/02/2026 | 07/02/2026 |  |
| 6 | - Researched DynamoDB Streams feature to capture real-time data modification events and forward signals to an independent analytical Lambda function.  | 07/03/2026 | 07/03/2026 | <https://000039.awsstudygroup.com/vi/>  |

### Week 11 Results:

* Completed the design specifications for the tiered file storage and the NoSQL data structure schema for DynamoDB, ensuring high query performance.
* Validated the feasibility of the Event-Driven architectural model, ensuring the system runs automatically in the background without requiring a monitoring server.
