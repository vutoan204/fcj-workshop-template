---
title: "Blog 3"
date: 2024-01-01
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# WHY YOU SHOULD NOT OPEN SSH PORT 22 FOR EC2? EXPLORING AWS SYSTEMS MANAGER SESSION MANAGER

When first learning AWS, many developers access their EC2 instances by opening port 22 in their Security Group, creating an SSH key pair, and then SSH-ing directly into the server.

While this approach is familiar and functional, it introduces several limitations and risks in a production environment:
* **Key Management Overhead:** Managing, distributing, and rotating SSH private keys for multiple developers.
* **IP Whitelisting Complexity:** Constantly updating Security Group rules to permit developer IPs.
* **Network Complexity:** Needing a Bastion Host if the EC2 instances reside inside a private subnet.
* **Security Risks:** Publicly exposing port 22 increases the attack surface, leaving instances vulnerable to brute-force attacks.

AWS Systems Manager Session Manager provides a more secure approach to accessing EC2 instances. With Session Manager, administrators can establish secure shell sessions with EC2 instances directly through the AWS Console or AWS CLI without opening inbound ports, needing a Bastion Host, or managing traditional SSH key pairs.

---

### Key Advantages of AWS Systems Manager Session Manager

1. **Centralized Access Control via IAM:** Instead of sharing and distributing private key files, administrators grant or revoke access using standard IAM policies. This aligns with modern cloud operations and enforces the principle of least privilege.
2. **No Open Inbound Ports:** Session Manager operates via outbound connections from the instance to AWS Systems Manager. Port 22 does not need to be opened in Security Groups.
3. **No Bastion Hosts Needed:** Connect directly to instances in private subnets without requiring or maintaining intermediate bastion servers.
4. **Detailed Audit Logging:** Standard sessions allow full logging and auditing of command history to Amazon CloudWatch Logs or Amazon S3.

---

### Prerequisites to Use Session Manager

To use Session Manager correctly, the EC2 instance must meet the following requirements:
* **SSM Agent:** The SSM Agent must be installed and running on the EC2 instance (pre-installed by default on many AWS AMIs like Amazon Linux 2/2023).
* **IAM Role:** The instance must have an IAM Role/Instance Profile attached with Systems Manager permissions (e.g., the AWS-managed policy `AmazonSSMManagedInstanceCore`).
* **Outbound Connectivity:** The instance must have outbound HTTPS port 443 connectivity to the Systems Manager endpoints.
* **Private Subnet Access:** If the instance is in a private subnet with no internet access (no NAT Gateway), you can set up VPC Endpoints (AWS PrivateLink) to connect securely to Systems Manager.

---

### Session Types and Considerations

Session Manager supports multiple connection modes:
* **Standard Session:** Provides shell access directly. All command execution can be fully logged and audited. No SSH keys are used.
* **SSH Tunneling / Port Forwarding:** Session Manager acts as a secure tunnel (e.g., using SSH over Session Manager or port forwarding for database access). In this mode, commands executed inside the tunnel cannot be logged by Session Manager.

### Conclusion

In the cloud, security is not just about strong passwords or firewalls; sometimes the best practice is not to expose administrative ports to the public internet in the first place. AWS Systems Manager Session Manager is a critical pattern for building secure, production-ready environments on AWS.

### Architecture Diagram
![AWS Systems Manager Session Manager Architecture](/images/3-BlogsPosted/blog3.png)

### Facebook Post & References
* **Original Blog Post on Facebook:** [AWS Study Group Post #2207318586699768](https://www.facebook.com/groups/awsstudygroupfcj/permalink/2207318586699768/)
* **Official References:**
  1. [What is AWS Systems Manager?](https://docs.aws.amazon.com/systems-manager/latest/userguide/what-is-systems-manager.html)
  2. [Step 6: (Optional) Use AWS PrivateLink to set up a VPC endpoint for Session Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager-access-control-vpc-endpoints.html)
  3. [AWS Systems Manager Session Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager.html)