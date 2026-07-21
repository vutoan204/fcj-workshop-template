---
title: "Week 9 Worklog"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.9. </b> "
---



### Week 9 Objectives:
* Finalize the comprehensive end-to-end system architecture diagram for the smart image processing platform across Frontend, Edge, and Backend layers.
* Design external security perimeter solutions integrating Amazon Amplify, AWS WAF malware filtering/rate limiting, and API Gateway protection.
* Clarify Amazon Cognito user authentication mechanisms (AccessToken/IDToken), User Pool group management, and token verification flows.
* Align architecture components and data contracts with team members to establish a standard framework for Frontend-Backend integration.

### Tasks Completed This Week:
| Day | Actual Work Content | Start Date | End Date | Reference / Resource |
| --- | --- | --- | --- | --- |
| 2 | - Design the Frontend section on the diagram, linking data flows from Amplify through WAF to filter malicious requests before forwarding them to API Gateway. | 06/15/2026 | 06/15/2026 | <https://aws.amazon.com/architecture/> |
| 3 | - Research and complete the architecture diagram connecting Backend service branches including Lambda, S3, and DynamoDB, with integrated Amazon Rekognition features. | 06/16/2026 | 06/16/2026 | <https://aws.amazon.com/architecture/> |
| 4 | - Researched configuration rules of AWS WAF malware filtering in detail and compared documentation on static resource compression/optimization when distributed via Amplify. | 06/17/2026 | 06/17/2026 | <https://000026.awsstudygroup.com/vi/> |
| 5 | - Studied Amazon Cognito documentation to clarify token generation and verification mechanisms (AccessToken/IDToken) and user group segmentation within the User Pool. <br> - Initialize and configure an Amazon Cognito User Pool, setting up custom attributes and secure password validation rules. | 06/18/2026 | 06/18/2026 | <https://000141.awsstudygroup.com/vi/> |
| 6 | - Discussed with the Frontend team member to thoroughly explain the components and data flows on the diagram, ensuring they understand the API calling structure. | 06/19/2026 | 06/19/2026 |  |

### Week 9 Results:
* The overall system architecture diagram has been completed, standardizing 100% of the connection flows and approved by the entire team.
* Gained a solid grasp of the external security perimeter solutions (WAF/Amplify) and the identity management method using Cognito to apply in the API design phase.
* Resolved all architectural questions for team members, providing the Frontend team with a standard framework to start their work.