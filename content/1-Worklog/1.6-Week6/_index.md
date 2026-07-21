---
title: "Week 6 Worklog"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.6. </b> "
---
### Week 6 Objectives:
* Research advanced AWS storage solutions (Glacier, Snow Family) and practice VM Import/Export for server migration.
* Deploy enterprise Multi-AZ FSx for Windows File Server (Deduplication, Shadow copies) and Security Hub compliance auditing.
* Automate EC2 operations via tagged Lambda functions with Slack notifications, implement condition-restricted IAM Switch Role policies, and configure KMS encryption with CloudTrail/Athena logging.

### Tasks to be carried out this week:
| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - **Advanced Storage & Infrastructure Migration:** <br>&emsp; + Module 04-01 -> 04-04: AWS Storage Core Theory <br>&emsp; + Module 04-Lab14-01 -> Lab14-05: VM Import/Export migrating on-premises VMs to AWS | 05/25/2026 | 05/25/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3 | - **Enterprise File Systems Implementation:** <br>&emsp; + Module 04-Lab25-2.2 -> Lab25-13: Configuring Multi-AZ FSx Windows File Server, shadow copies, quotas, and deduplication | 05/26/2026 | 05/26/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4 | - **Infrastructure Security & Operations Automation:** <br>&emsp; + Module 05-01 -> 05-08: AWS Security Core Theory <br>&emsp; + Module 05-Lab18-02 -> Lab18-04: Security compliance assessment using AWS Security Hub <br>&emsp; + Module 05-Lab22-2.1 -> Lab22-7: AWS Lambda auto start/stop EC2 based on tags with Slack Webhook notifications | 05/27/2026 | 05/27/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5 | - **Resource Management & Advanced IAM Permissions:** <br>&emsp; + Module 05-Lab27-2.1.1 -> Lab27-4: Resource Tags & Resource Group Management <br>&emsp; + Module 05-Lab28-2.1 -> Lab28-6: IAM Policy, Role, and Switch Roles execution <br>&emsp; + Module 05-Lab44-2 -> Lab44-5: Restricting Switch Role permissions based on Source IP and Time windows | 05/28/2026 | 05/28/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6 | - **Data Encryption & Activity Auditing:** <br>&emsp; + Module 05-Lab33-2.1 -> Lab33-7: AWS KMS Encryption for S3, activity logging with AWS CloudTrail, and log querying via Amazon Athena <br>&emsp; + Module 05-Lab48-1.1 -> Lab48-4: Differentiating Access Keys vs. IAM Instance Profiles (IAM Roles) on EC2 | 05/29/2026 | 05/29/2026 | <https://cloudjourney.awsstudygroup.com/> |

### Week 6 Achievements:
* Successfully migrated virtual machines from an on-premises environment to AWS via VM Import/Export.
* Built an enterprise shared file storage system using FSx for Windows File Server with Data Deduplication and Shadow Copies.
* Automated EC2 start/stop schedules using EventBridge and Lambda based on Resource Tags with real-time Slack notifications.
* Audited AWS security compliance using Security Hub and implemented condition-based IAM Switch Role access (IP & Time restricted).
* Encrypted S3 data with AWS KMS, logged API activities via CloudTrail, and performed audit log queries using Amazon Athena.
