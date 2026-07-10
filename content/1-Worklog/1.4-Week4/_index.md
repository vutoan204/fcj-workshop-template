---
title: "Week 4 Worklog"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.4. </b> "
---
### Week 4 Objectives:
* Complete VPC networking deployment, configure internet routing via NAT Gateway & IGW, and set up Security Groups.
* Launch EC2 instances in subnets and configure secure connection methods using EC2 Instance Connect Endpoint.

### Tasks to be carried out this week:
| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - **Configure VPC Routing & Security:** <br>&emsp; + Module 02-Lab03-03.3 - Create an Internet Gateway <br>&emsp; + Module 02-Lab03-03.4 - Create Route Table for Outbound Internet Routing via Internet Gateway <br>&emsp; + Module 02-Lab03-03.5 - Create security groups | 05/08/2026 | 05/08/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3 | - **Launch Instances & Configure NAT Routing:** <br>&emsp; + Module 02-Lab03-04.1 - Create EC2 Instances in Subnets <br>&emsp; + Module 02-Lab03-04.2 - Test connection | 05/09/2026 | 05/09/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4 | - **Launch Instances & Configure NAT Routing (Continued):** <br>&emsp; + Module 02-Lab03-04.3 - Create NAT Gateway <br>&emsp; + Module 02-Lab03-04.5 - EC2 Instance Connect Endpoint | 05/10/2026 | 05/10/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5 | - **Review and Optimize Network Topology:** <br>&emsp; + Verify routing policies between public and private subnets <br>&emsp; + Test inbound and outbound traffic isolation protocols | 05/11/2026 | 05/11/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6 | - **Security Assessment of Core Network:** <br>&emsp; + Audit inbound/outbound rules for newly created Security Groups <br>&emsp; + Verify secure connections using EC2 Instance Connect Endpoint | 05/12/2026 | 05/12/2026 | <https://cloudjourney.awsstudygroup.com/> |

### Week 4 Achievements:
* Deployed a fully functional VPC routing configuration allowing public subnets to access the Internet via IGW and private subnets via NAT Gateway.
* Launched EC2 instances successfully inside multiple VPC subnets.
* Configured and used EC2 Instance Connect Endpoint to establish secure SSH/RDP sessions without requiring public IPs or Bastion Hosts.