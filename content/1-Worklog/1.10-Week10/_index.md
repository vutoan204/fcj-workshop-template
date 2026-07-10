---
title: "Week 10 Worklog"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 1.10. </b> "
---



### Week 10 Objectives:

* Conduct in-depth research on the Endpoint architecture for the secure API system and find the most optimal solution to grant direct image upload permissions without exposing storage bucket details.

### Tasks Completed This Week:
| Day | Work Content | Start Date | End Date | Reference / Resource |
| --- | --- | --- | --- | --- |
| 2 | - Studied API Gateway documentation to compile a list of REST API paths and define the input/output JSON data structures for image processing tasks. | 06/22/2026 | 06/22/2026 | <https://docs.aws.amazon.com/apigateway/> |
| 3 | - Researched compute resource optimization solutions with AWS Lambda (Serverless) and built an IAM permission matrix to strictly limit function privileges. | 06/23/2026 | 06/23/2026 | <https://docs.aws.amazon.com/lambda/> |
| 4 | - Explored technical documentation on S3 Pre-signed URLs to find a solution for generating time-limited file upload signatures, ensuring S3 Bucket security. | 06/24/2026 | 06/24/2026 | <https://docs.aws.amazon.com/AmazonS3/latest/userguide/ShareObjectPreSignedURL.html> |
| 5 | - Synthesized documentation guiding the automatic Token verification logic via API Gateway Authorizer before requests are forwarded to the Backend. | 06/25/2026 | 06/25/2026 | <https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-use-lambda-authorizer.html> |
| 6 | - Assisted the Backend developer in clarifying the integration mechanism of Pre-signed URL signature generation into Lambda functions and handling expired Client tokens. | 06/26/2026 | 06/26/2026 |  |

### Week 10 Results:

* Finalized the complete architecture design for the secure REST API gateway, featuring an automated user access authorization mechanism.
* Successfully built a technical solution for granting direct image upload permissions to the storage bucket via Pre-signed URLs, minimizing data exposure risks.
* Compiled a comprehensive guide on Backend logic processing details, serving as a solid development foundation for the team.