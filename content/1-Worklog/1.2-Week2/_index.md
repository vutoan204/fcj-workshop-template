---
title: "Week 2 Worklog"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.2. </b> "
---
### Week 2 Objectives:
* Understand core architectural concepts and network security configurations of AWS Virtual Private Cloud (VPC).
* Design and practically implement a secure, multi-tier custom VPC network with public and private subnets.
* Configure Internet Gateway (IGW), NAT Gateway, Route Tables, Security Groups, Network ACLs, and EC2 Instance Connect Endpoint.

### Tasks to be carried out this week:
| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - **Learn VPC fundamental concepts:** <br>&emsp; + Module 02-01 - AWS Virtual Private Cloud Overview <br>&emsp; + Module 02-02 - VPC Security Essentials and Multi-VPC features <br>&emsp; + Module 02-03 - Advanced networking connectivity: VPN, DirectConnect, LoadBalancer | 04/27/2026 | 04/27/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3 | - **Practice building a VPC network (Core Infrastructure):** <br>&emsp; + Module 02-Lab03-01 - Initializing Amazon VPC and AWS VPN Site-to-Site configuration <br>&emsp; + Module 02-Lab03-01.1 - Designing and allocating network Subnets <br>&emsp; + Module 02-Lab03-01.2 - Configuring Route Tables for traffic control | 04/28/2026 | 04/28/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4 | - **Practice building a VPC network (Gateway Integration):** <br>&emsp; + Module 02-Lab03-01.3 / Lab03-03.3 - Creating and attaching an Internet Gateway (IGW) <br>&emsp; + Module 02-Lab03-01.4 / Lab03-04.3 - Setting up a NAT Gateway for private network out-bound access <br>&emsp; + Module 02-Lab03-03.4 - Route Table for Outbound Internet Routing | 04/29/2026 | 04/29/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5 | - **Practice network security and traffic filtering:** <br>&emsp; + Module 02-Lab03-02.1 / Lab03-03.5 - Configuring stateful instance-level security via Security Groups <br>&emsp; + Module 02-Lab03-02.2 - Establishing stateless subnet-level boundaries via Network ACLs | 04/30/2026 | 04/30/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6 | - **Launch EC2 Instances & Configure Endpoint Connections:** <br>&emsp; + Module 02-Lab03-04.1 - Create EC2 Instances in Subnets <br>&emsp; + Module 02-Lab03-04.2 - Test connection <br>&emsp; + Module 02-Lab03-04.5 - EC2 Instance Connect Endpoint configuration | 05/01/2026 | 05/01/2026 | <https://cloudjourney.awsstudygroup.com/> |

### Week 2 Achievements:
* Acquired a solid structural understanding of VPC design patterns, including routing logic with Route Tables, NAT Gateways, and Internet Gateways.
* Mastered granular security mechanisms: stateful filtering via Security Groups and stateless perimeter traffic filtering using Network ACLs.
* Built a complete multi-tier custom VPC containing isolated Public subnets and Private subnets.
* Configured EC2 Instance Connect Endpoint to establish secure SSH/RDP sessions without public IPs or Bastion Hosts.
