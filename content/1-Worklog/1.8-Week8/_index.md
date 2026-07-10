---
title: "Week 8 Worklog"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.8. </b> "
---
### Week 8 Objectives:

* Deeply research advanced AWS storage solutions (S3 Access Points, Storage Classes, Glacier, Snow Family) and practice on-premises server migration using VM Import/Export.
* Deploy a high-performance shared file system using Multi-AZ FSx for Windows File Server (SSD/HDD FSx, Shadow copies, Quotas, Data deduplication) and AWS Storage Gateway.
* Study AWS security core concepts (Shared Responsibility Model, IAM, Cognito, AWS Organizations, Identity Center, KMS, Security Hub).
* Practice resource management using AWS Tags and automate server operations (Lambda auto start/stop EC2 based on tags with Slack notifications).
* Implement granular access control with IAM Policies/Roles, Switch Role restricted by IP/Time conditions, and KMS encryption combined with AWS CloudTrail auditing and Amazon Athena log queries.

### Tasks to be carried out this week:
| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - **Advanced Storage & Infrastructure Migration:** <br>&emsp; + Module 04-01 -> 04-04: AWS Storage Core Theory <br>&emsp; + Module 04-Lab14-01 -> Lab14-05: VM Import/Export migrating on-premises VMs to AWS <br>&emsp; + Module 04-Lab13-02.1 -> Lab13-06: Advanced AWS Backup configurations | 06/08/2026 | 06/08/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3 | - **Enterprise File Systems Implementation:** <br>&emsp; + Module 04-Lab24-2.1 -> Lab24-3: Storage Gateway and mounting local File Shares <br>&emsp; + Module 04-Lab25-2.2 -> Lab25-13: Configuring Multi-AZ FSx Windows File Server, shadow copies, quotas, and deduplication <br>&emsp; + Module 04-Lab57-2.1 -> Lab57-11: Advanced S3 Static Website & CloudFront setup | 06/09/2026 | 06/09/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4 | - **Infrastructure Security & Operations Automation:** <br>&emsp; + Module 05-01 -> 05-08: AWS Security Core Theory <br>&emsp; + Module 05-Lab18-02 -> Lab18-04: Security compliance assessment using AWS Security Hub <br>&emsp; + Module 05-Lab22-2.1 -> Lab22-7: Developing AWS Lambda to auto start/stop EC2 based on tags with Slack Webhook notifications | 06/10/2026 | 06/10/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5 | - **Resource Management & Advanced IAM Permissions:** <br>&emsp; + Module 05-Lab27-2.1.1 -> Lab27-4: Resource Tags & Resource Group Management <br>&emsp; + Module 05-Lab28-2.1 -> Lab28-6: IAM Policy, Role, and Switch Roles execution <br>&emsp; + Module 05-Lab30-3 -> Lab30-6: Creating a Limited User account and testing access policies <br>&emsp; + Module 05-Lab44-2 -> Lab44-5: Restricting Switch Role permissions based on Source IP and Time windows | 06/11/2026 | 06/11/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6 | - **Data Encryption & Activity Auditing:** <br>&emsp; + Module 05-Lab33-2.1 -> Lab33-7: AWS KMS Encryption for S3, activity logging with AWS CloudTrail, and log querying via Amazon Athena <br>&emsp; + Module 05-Lab48-1.1 -> Lab48-4: Differentiating Access Keys vs. IAM Instance Profiles (IAM Roles) on EC2 instances | 06/12/2026 | 06/12/2026 | <https://cloudjourney.awsstudygroup.com/> |

### Week 8 Achievements:

* Successfully migrated virtual machines from a local virtualized environment (On-premises) to run directly on the AWS Cloud via the VM Import/Export workflow.
* Built an enterprise-grade shared file storage system using FSx for Windows File Server, enabling Data Deduplication to optimize disk space (saving up to 50-60%) and configuring Shadow Copies for quick user-driven file recovery.
* Automated off-hours EC2 instance operations using an AWS Lambda function triggered by Amazon EventBridge Rules based on specific Resource Tags, coupled with instant status updates sent to a Slack channel.
* Evaluated and audited AWS account security compliance metrics against industry best practices using AWS Security Hub.
* Implemented advanced IAM governance by defining strict condition keys within IAM Policies to restrict Switch Role operations based on the user's source IP addresses and authorized working hours.
* Secured data at rest inside Amazon S3 using customer-managed AWS KMS keys, recorded comprehensive system API activities via AWS CloudTrail, and performed seamless audit log compliance analysis using raw SQL queries directly inside Amazon Athena.
* Mastered core security best practices: utilizing IAM Roles assigned to EC2 instances (IAM Instance Profiles) instead of hardcoding static Access Keys/Secret Keys within source code or application environments.
