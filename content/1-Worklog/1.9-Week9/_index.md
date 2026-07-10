---
title: "Week 9 Worklog"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.9. </b> "
---



### Week 9 Objectives:

* Focus on finalizing the overall system architecture diagram for the entire image processing system and clarifying the frontend distribution method along with the external user authentication mechanism.

### Tasks Completed This Week:
| Day | Actual Work Content | Start Date | End Date | Reference / Resource |
| --- | --- | --- | --- | --- |
| 2 | Drafted and finalized the Frontend partition on the diagram; linked the data flows from Amplify through WAF to block and filter malicious requests before forwarding them to API Gateway. | 06/15/2026 | 06/15/2026 | <https://docs.aws.amazon.com/amplify/> |
| 3 | Drew the remaining half of the diagram, connecting Backend service branches including Lambda, S3, DynamoDB, and integrated the recognition structure of Amazon Rekognition. | 06/16/2026 | 06/16/2026 | <https://aws.amazon.com/architecture/> |
| 4 | Researched configuration rules of AWS WAF malware filtering in detail and compared documentation on static resource compression/optimization when distributed via Amplify. | 06/17/2026 | 06/17/2026 | <https://docs.aws.amazon.com/waf/> |
| 5 | Studied Amazon Cognito documentation to clarify token generation and verification mechanisms (AccessToken/IDToken) and user group segmentation within the User Pool. | 06/18/2026 | 06/18/2026 | <https://docs.aws.amazon.com/cognito/> |
| 6 | Discussed with the Frontend team member to thoroughly explain the components and data flows on the diagram, ensuring they understand the API calling structure. | 06/19/2026 | 06/19/2026 |  |

### Week 9 Results:
* The overall system architecture diagram has been completed, standardizing 100% of the connection flows and approved by the entire team.
* Gained a solid grasp of the external security perimeter solutions (WAF/Amplify) and the identity management method using Cognito to apply in the API design phase.
* Resolved all architectural questions for team members, providing the Frontend team with a standard framework to start their work.