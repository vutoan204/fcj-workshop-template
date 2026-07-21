---
title: "Week 10 Worklog"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 1.10. </b> "
---



### Week 10 Objectives:
* Research secure REST API Gateway Endpoint architecture and build an automated user access authorization mechanism using Cognito Authorizer.
* Build a technical solution for granting direct, time-limited image upload permissions to S3 buckets via S3 Presigned URLs without exposing storage bucket details.
* Optimize serverless compute resources using AWS Lambda and construct an IAM permission matrix to strictly enforce least-privilege function execution.
* Synthesize Backend logic details and token validation rules to establish a solid development foundation for the team.

### Tasks Completed This Week:
| Day | Work Content | Start Date | End Date | Reference / Resource |
| --- | --- | --- | --- | --- |
| 2 | - Studied API Gateway documentation to compile a list of REST API paths and define the input/output JSON data structures for image processing tasks. | 06/22/2026 | 06/22/2026 | <https://000135.awsstudygroup.com/vi/> |
| 3 | - Researched compute resource optimization solutions with AWS Lambda (Serverless) and built an IAM permission matrix to strictly limit function privileges. | 06/23/2026 | 06/23/2026 | <https://000022.awsstudygroup.com/vi/> |
| 4 | - Explore object storage with Amazon S3 and archival storage with S3 Glacier. <br> - Investigate advanced data security control structures: S3 Versioning, Lifecycle policies, and Bucket policies. | 06/24/2026 | 06/24/2026 | https://000078.awsstudygroup.com/vi/ |
| 5 | - Synthesized documentation guiding the automatic Token verification logic via API Gateway Authorizer before requests are forwarded to the Backend. | 06/25/2026 | 06/25/2026 | <https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-use-lambda-authorizer.html> |
| 6 | - Assist team members with Backend code configuration, clarifying the Pre-signed URL generation process in Lambda functions and the error handling mechanism for expired Client Tokens. | 06/26/2026 | 06/26/2026 |  |

### Week 10 Results:

* Finalized the complete architecture design for the secure REST API gateway, featuring an automated user access authorization mechanism.
* Successfully built a technical solution for granting direct image upload permissions to the storage bucket via Pre-signed URLs, minimizing data exposure risks.
* Compiled a comprehensive guide on Backend logic processing details, serving as a solid development foundation for the team.