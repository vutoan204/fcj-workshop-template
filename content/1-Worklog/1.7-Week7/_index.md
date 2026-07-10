---
title: "Week 7 Worklog"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.7. </b> "
---
### Week 7 Objectives:
* Set up a highly secure static website on S3 combined with Amazon CloudFront CDN.
* Learn and configure S3 Bucket Versioning and Cross-Region Replication (CRR) to support disaster recovery (DR) plans.

### Tasks to be carried out this week:
| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - **Implement S3 Static Website:** <br>&emsp; + Module 03-Lab57-02.1 - Create S3 bucket <br>&emsp; + Module 03-Lab57-02.2 - Load data <br>&emsp; + Module 03-Lab57-03 - Enable static website feature | 06/01/2026 | 06/01/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3 | - **Configure S3 Public Access:** <br>&emsp; + Module 03-Lab57-04 - Configuring public access block <br>&emsp; + Module 03-Lab57-05 - Configuring public objects <br>&emsp; + Module 03-Lab57-06 - Test website | 06/02/2026 | 06/02/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4 | - **Configure CloudFront CDN Integration:** <br>&emsp; + Module 03-Lab57-07.1 - Block all public access <br>&emsp; + Module 03-Lab57-07.2 - Config Amazon CloudFront <br>&emsp; + Module 03-Lab57-07.3 - Test Amazon Cloudfront | 06/03/2026 | 06/03/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5 | - **Implement Data Protection & Replication:** <br>&emsp; + Module 03-Lab57-08 - Bucket Versioning <br>&emsp; + Module 03-Lab57-09 - Move objects | 06/04/2026 | 06/04/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6 | - **Finalize Cross-Region Replication & Cleanup:** <br>&emsp; + Module 03-Lab57-10 - Replication Object multi Region <br>&emsp; + Module 03-Lab57-11 - Clean up resources | 06/05/2026 | 06/05/2026 | <https://cloudjourney.awsstudygroup.com/> |

### Week 7 Achievements:
* Deployed an S3 hosted static site, restricted direct public traffic on the bucket, and routed requests through CloudFront CDN to safeguard data and accelerate load speeds globally.
* Implemented S3 Bucket Versioning to store previous file copies and track changes.
* Set up Cross-Region Replication (CRR) to automatically mirror files to a secondary region, supporting robust disaster recovery (DR) strategies.