---
title: "Blogs Posted"
date: 2024-01-01
weight: 3
chapter: false
pre: " <b> 3. </b> "
---



This section will list and introduce the blogs you have posted to [AWS Study Group](https://www.facebook.com/groups/awsstudygroupfcj). For example:

###  [Blog 1 - EFFICIENT IMAGE OPTIMIZATION WITH AMAZON CLOUDFRONT AND AWS LAMBDA](3.1-Blog1/)
This architecture utilizes Amazon CloudFront and AWS Lambda to deliver an efficient, on-the-fly image optimization solution. By storing only a single high-quality original image in Amazon S3, the system dynamic compresses, resizes, and converts images into next-gen formats like WebP or AVIF only upon user request. This serverless, on-demand processing eliminates unnecessary storage costs for pre-rendered image variations. Furthermore, optimized assets are cached at CloudFront edge locations, significantly reducing latency, data transfer bandwidth, and improving web performance metrics like Google's Core Web Vitals.

###  [Blog 2 - DYNAMIC IMAGE PROCESSING ON AWS WITH AMAZON SAGEMAKER AND AMAZON BEDROCK](3.2-Blog2/)
This hybrid architecture combines Amazon SageMaker and Amazon Bedrock to build a cost-effective, high-performance generative AI image editing tool. For object segmentation and context filling, open-source models like Meta's SAM and LaMa are hosted locally on SageMaker Multi-Model Endpoints (MME), optimizing GPU memory sharing via NVIDIA Triton Server. For advanced text-guided inpainting and canvas outpainting, the system shifts to serverless Bedrock APIs invoking massive foundation models like SDXL 1.0 and Amazon Titan Image Generator. This approach strikes an ideal balance for enterprises by maintaining low-latency execution for core pipelines while tapping into heavy generative models completely on-demand.

###  [Blog 3 - WHY YOU SHOULD NOT OPEN SSH PORT 22 FOR EC2? EXPLORING AWS SYSTEMS MANAGER SESSION MANAGER](3.3-Blog3/)
This architecture highlights AWS Systems Manager Session Manager as a secure alternative to opening traditional SSH Port 22 on EC2 instances. By relying on outbound HTTPS connections to AWS Systems Manager, it completely eliminates the security risks of exposed administrative ports, the complexity of public IP whitelisting, and the need for Bastion Hosts. Access control is centralized using standard IAM policies instead of cumbersome static SSH key management, enforcing strict least-privilege principles. The system requires an active SSM Agent and appropriate IAM execution roles on the instance to establish secure shell sessions. Additionally, it offers comprehensive audit trails by automatically logging all executed commands directly to Amazon CloudWatch or Amazon S3.