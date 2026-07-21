---
title: "Tổng quan Workshop"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

# Tổng quan Smart Image Platform

Smart Image Platform là ứng dụng serverless cho phép đăng ký tài khoản, tải ảnh trực tiếp lên S3 bằng presigned URL, tạo thumbnail/ảnh resized và phân tích nội dung bằng Amazon Rekognition.

## Kiến trúc đang được triển khai

- **Amazon Cognito:** User Pool, SPA app client và hai group `admin`/`user`.
- **Amazon S3:** raw bucket và processed bucket đều private, mã hóa SSE-S3.
- **Amazon DynamoDB:** ba bảng `Images`, `UserQuotas`, `UserProfiles`. Single-table pattern chỉ áp dụng bên trong bảng `Images`.
- **AWS Lambda:** `ApiHandler`, `ImageProcessor`, `AiAnalyzer`; CDK hiện còn tạo một custom `Authorizer` nhưng API sử dụng Cognito User Pool Authorizer.
- **Amazon API Gateway:** REST API với các resource/method khai báo rõ ràng. `/v1/images/public` là public; các route nghiệp vụ khác dùng Cognito authorizer theo cấu hình.
- **Amazon Rekognition:** phát hiện nhãn và nội dung cần kiểm duyệt.
- **Amazon SQS:** DLQ cho `ImageProcessor` và DynamoDB event source của `AiAnalyzer`.
- **Amazon CloudWatch và SNS:** log, metric, dashboard và cảnh báo vận hành.
- **AWS WAF:** bảo vệ API Gateway.
- **AWS Amplify Hosting:** build và deploy React client từ nhánh GitHub của môi trường.
- **S3 presigned GET URL:** phân phối ảnh ở trạng thái hiện tại. CloudFront đang bị tắt trong Storage stack.

## Hai luồng của workshop

### Phần I – AWS CDK

Đây là luồng triển khai chính. Sáu stack được tạo theo môi trường `staging` hoặc `production`: Storage, Database, Auth, API, Monitoring và Frontend.

### Phần II – AWS Console (tùy chọn)

Phần Console chỉ minh họa cách các cấu hình tương đương xuất hiện trong giao diện AWS. Một số màn hình không hiển thị toàn bộ thuộc tính mà CDK khai báo; khác biệt sẽ được ghi chú ngay tại bước liên quan.

> **Quy ước ảnh:** Ảnh chụp dùng tài nguyên `staging`. Tên, ID và hậu tố có thể khác giữa tài khoản hoặc lần deploy; cần đối chiếu theo loại tài nguyên và cấu hình thay vì sao chép nguyên tên.
