---
title: "Product Showcase"
date: 2026-07-21
weight: 8
chapter: false
pre: " <b> 5.8. </b> "
---

# Smart Image Platform in the live environment

This section presents the deployed Smart Image Platform running on a real domain. The screenshots are extracted from the system demonstration video rather than mocked AWS Console data.

> Production can use branch, stage, and resource names that differ from the `staging` Console walkthrough. The application architecture and core processing flow still correspond to the CDK project.

## Access information

| Item | Link |
|---|---|
| Application | [https://www.minphstudio.com/](https://www.minphstudio.com/) |
| Source code | [hngluc/AWS-Project](https://github.com/hngluc/AWS-Project) |
| Demo video | [Google Drive video folder](https://drive.google.com/drive/folders/1c4NO1Vn2drC_M_2BfU2TzMpQmCSlOaOd?hl=vi) |

The full demonstration is approximately 2 minutes 49 seconds and covers registration, email verification, upload, AI processing, editing, search, and image sharing.

## 1. Sign-in and Community Gallery access

The application uses Amazon Cognito for registration, email confirmation, and sign-in. Visitors can choose **View Community Gallery** without signing in; My Gallery, upload, and management operations require an authenticated session.

![Sign-in screen on minphstudio.com](/images/5-Workshop/5.8-Product-Showcase/live_sign_in.png)

## 2. Direct upload and asynchronous processing

The Upload screen accepts drag-and-drop or selected JPEG, PNG, WEBP, and GIF files up to 25 MB. After the API issues a presigned URL, the browser uploads the file directly to the raw S3 bucket.

The **Processing Queue** displays each file's progress. In the screenshot, upload has completed and the file is in **AI Processing**. The **Connected to AWS** badge confirms that the frontend is using the AWS backend rather than demo mode.

![An image in the Processing Queue](/images/5-Workshop/5.8-Product-Showcase/upload_processing.png)

The flow behind the interface is:

1. API Handler creates metadata and a presigned PUT URL.
2. The browser uploads the original directly to Amazon S3.
3. An S3 Event invokes Image Processor to generate resized output, a thumbnail, and EXIF metadata.
4. DynamoDB Streams invokes AiAnalyzer.
5. Amazon Rekognition produces object and moderation labels.
6. Final metadata is updated so the frontend can display `COMPLETED`.

## 3. Image Insights and AI labels

After the pipeline completes, **Image Insights & Controls** combines processing state, visibility, upload time, size, EXIF, and detected objects. Each AI label includes a confidence score to distinguish stronger and weaker matches.

![EXIF and Rekognition-generated AI labels](/images/5-Workshop/5.8-Product-Showcase/ai_image_insights.png)

From this view, a user can:

- Switch an image between `PRIVATE` and `PUBLIC`.
- Download the original through an expiring presigned URL.
- Open the basic editor or WebGL Shader Lab.
- Delete the image and its related objects when no longer needed.

## 4. Image editing and Save as Copy

The basic editor provides brightness, contrast, saturation, ±90° rotation, and horizontal/vertical flipping. **Save as Copy** creates a new image instead of overwriting the original, preserving the initial gallery asset.

![Brightness, contrast, saturation, and transform controls](/images/5-Workshop/5.8-Product-Showcase/image_editor.png)

## 5. WebGL Shader Lab

WebGL Shader Lab can stack up to five GPU effects. Available effects include grayscale, color tint, pixelation, chromatic aberration, wave distortion, VHS/glitch, halftone, kaleidoscope, and water ripple. Effect order and parameters can be adjusted before saving the result as a copy.

![Stacked effects in WebGL Shader Lab](/images/5-Workshop/5.8-Product-Showcase/webgl_shader_lab.png)

Editing runs in the frontend. A new stored image is created only after **Save as Copy** is selected.

## 6. Managing images in My Gallery

My Gallery displays images owned by the signed-in account together with processing state and visibility. Users can open details, select multiple images for bulk deletion, refresh processing state, or continue editing a completed image.

![User-owned images in My Gallery](/images/5-Workshop/5.8-Product-Showcase/my_gallery.png)

Thumbnails and previews use expiring URLs; the raw and processed buckets do not require public-read policies.

## 7. AI label search

AI Tag Search uses labels written to metadata by Amazon Rekognition. In the video, the query `city` returns images carrying that label without requiring manual tagging during upload.

![AI Tag Search results for city](/images/5-Workshop/5.8-Product-Showcase/ai_tag_search.png)

Search results continue to enforce visibility and access rules so another account's private images are not exposed.

## 8. Community Gallery

Community Gallery contains `PUBLIC` images that completed processing and satisfy moderation conditions. Visitors can browse it without authentication, while visibility changes and deletion remain authenticated owner operations.

![Community Gallery in the live deployment](/images/5-Workshop/5.8-Product-Showcase/community_gallery.png)

## Demonstrated outcome

The live deployment verifies the end-to-end path from the frontend to the AWS backend:

- Cognito registration, email verification, and authenticated sessions.
- Direct S3 uploads through presigned URLs.
- Lambda/Sharp thumbnail, resized-image, and EXIF processing.
- Amazon Rekognition labels and moderation analysis.
- My Gallery, Community Gallery, AI Tag Search, and visibility controls.
- Basic editing, WebGL Shader Lab, download, and bulk deletion.

The next [Testing and Verification](../5.9-Testing-Validation/) section explains how to correlate these operations with S3, DynamoDB, Lambda Logs, CloudWatch metrics, and alarms.
