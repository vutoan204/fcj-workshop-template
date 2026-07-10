---
title: "Blog 1"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# EFFICIENT IMAGE OPTIMIZATION WITH AMAZON CLOUDFRONT AND AWS LAMBDA

Images are often the heaviest resources on modern websites. Loading large images not only slows down page load times but also directly affects user experience and SEO rankings, especially Google's Largest Contentful Paint (LCP) metric.

To address this challenge, AWS introduced a serverless image optimization solution combining Amazon CloudFront, AWS Lambda, and Amazon S3. This architecture enables automatic image processing on-the-fly while minimizing distribution and storage costs.

### Core Architecture Idea
Instead of manually creating and storing multiple resized versions of each image in an Amazon S3 bucket (which wastes storage space), the system only stores a single high-quality original image. Format conversion and image resizing are performed automatically on-the-fly when requested by the user and then cached for subsequent visits.

### Architectural Workflow

1. **User Request:** The user accesses the website, and the browser requests an image with specific width, height, and format parameters suitable for the client's screen size via Amazon CloudFront.
2. **CloudFront Cache Check:** Amazon CloudFront checks if the requested image version is already cached:
   * **Cache Hit:** The image is immediately returned to the user at maximum speed.
   * **Cache Miss:** The request is forwarded to an AWS Lambda function.
3. **Lambda Execution:** The Lambda function fetches the high-quality original image from the Amazon S3 bucket.
4. **On-the-Fly Processing:** The function processes the image (cropping, compressing, resizing, and converting to next-gen formats like WebP or AVIF).
5. **Return and Cache:** The processed image is sent back to CloudFront. CloudFront delivers the image to the user and simultaneously stores it in the cache for future requests.

### Key Advantages
* **Enhanced Performance:** Significantly reduces the page payload size, speeding up page rendering and improving Google's Core Web Vitals.
* **Storage and Bandwidth Cost Savings:** Eliminates the cost of storing numerous static image variations on S3. Additionally, serving lighter, optimized images reduces CloudFront's data transfer costs.
* **Seamless Integration:** Highly compatible with modern web frameworks, particularly matching the automatic image optimization capabilities of Next.js.

### Architecture Diagram
![Image Optimization Architecture](/images/3-BlogsPosted/blog1.png)

### Facebook Post & References
* **Original Blog Post on Facebook:** [AWS Study Group Post #2206231570141803](https://www.facebook.com/groups/awsstudygroupfcj/permalink/2206231570141803/)
* **Official AWS Blog Reference:** [Image Optimization using Amazon CloudFront and AWS Lambda](https://aws.amazon.com/blogs/networking-and-content-delivery/image-optimization-using-amazon-cloudfront-and-aws-lambda/)