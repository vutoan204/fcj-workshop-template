---
title: "Week 11 Worklog"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 1.11. </b> "
---



### Week 11 Objectives:

* Define the tiered file storage structure combined with the NoSQL table model design, while evaluating the feasibility of the event-driven automated image processing workflow.

### Tasks Completed This Week:
| Day | Work Content | Start Date | End Date | Reference / Resource |
| --- | --- | --- | --- | --- |
| 2 | - Researched solutions to partition Object Storage into 2 isolated zones (original image bucket and compressed image bucket) and configured CORS cross-access blocking. | 06/29/2026 | 06/29/2026 | <https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html> |
| 3 | - Developed a NoSQL table configuration plan on DynamoDB, selecting the Partition Key and establishing secondary indexes to optimize image metadata search speeds. | 06/30/2026 | 06/30/2026 | <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.CoreComponents.html> |
| 4 | - Studied AWS documentation to apply the S3 Event Notification feature, designing an automated workflow to trigger Lambda functions as soon as a new file arrives in the original image bucket. | 07/01/2026 | 07/01/2026 | <https://docs.aws.amazon.com/AmazonS3/latest/userguide/NotificationHowTo.html> |
| 5 | - Conducted theoretical testing of the event-driven activation flow, reviewing the validity of sample data file structures passed between services. | 07/02/2026 | 07/02/2026 |  |
| 6 | - Researched DynamoDB Streams feature to capture real-time data modification events and forward signals to an independent analytical Lambda function. <br>&emsp; + Read Amazon Rekognition API documentation and studied the distribution architecture of the CloudFront CDN network. | 07/03/2026 | 07/03/2026 | <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Streams.html> <https://docs.aws.amazon.com/rekognition/latest/dg/labels.html> <https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Introduction.html> |

### Week 11 Results:

* Completed the design specifications for the tiered file storage and the NoSQL data structure schema for DynamoDB, ensuring high query performance.
* Validated the feasibility of the Event-Driven architectural model, ensuring the system runs automatically in the background without requiring a monitoring server.
