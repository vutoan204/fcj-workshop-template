---
title: "Tổng quan & Giới thiệu"
date: 2024-01-01 
weight: 1 
chapter: false
pre: " <b> 5.1. </b> "
---


# Tổng quan Workshop: Smart Image Platform

**Smart Image Platform** là một ứng dụng Serverless và hướng sự kiện (Event-Driven) trên AWS, được thiết kế để tự động hóa việc tải ảnh lên, xử lý ảnh (tạo thumbnail, thay đổi kích thước) và phân loại, gắn nhãn thông minh bằng AI.

Trong bài lab này, bạn sẽ học cách thiết lập và cấu hình toàn bộ hạ tầng của nền tảng **từng bước một bằng tay trên AWS Management Console**.

### Kiến trúc giải pháp

Nền tảng sử dụng các dịch vụ Serverless phối hợp chặt chẽ theo luồng dữ liệu số hóa tuần tự, được biểu diễn qua sơ đồ luồng hoạt động dưới đây:

![Smart Image Platform Architecture](/images/2-Proposal/platform_architecture.jpeg)

* **Các dịch vụ AWS chính được sử dụng:**
  * **Amazon S3:** Lưu trữ đối tượng (Object Storage) chứa ảnh thô tải lên (`raw-images-bucket`) và ảnh đã xử lý/thumbnail (`processed-images-bucket`).
  * **Amazon Cognito:** Quản lý tài khoản và xác thực người dùng (User Pool & App Client).
  * **Amazon DynamoDB:** Cơ sở dữ liệu NoSQL lưu trữ siêu dữ liệu (metadata) của hình ảnh (sử dụng Single-table design, bật GSI và DynamoDB Streams), quản lý hồ sơ người dùng và hạn mức tải lên (Quota).
  * **AWS Lambda:**
    * `api-handler`: Xử lý các request REST API (tạo presigned URL, CRUD DynamoDB, CRUD profile, kiểm tra hạn mức quota).
    * `image-processor`: Bắt sự kiện tải ảnh lên S3 raw (chỉ bắt các file có tiền tố `users/`) để tự động resize và lưu metadata cơ bản.
    * `ai-analyzer`: Bắt sự kiện thay đổi dữ liệu từ DynamoDB Stream để gọi Rekognition bóc nhãn AI.
  * **Amazon API Gateway:** Cung cấp các REST API cho phía Client.
  * **Amazon Rekognition:** Dịch vụ AI tự động phân tích hình ảnh (nhận diện vật thể, kiểm duyệt nội dung nhạy cảm).
  * **AWS Amplify:** Lưu trữ và triển khai ứng dụng frontend React.
  * **S3 Presigned GET URLs:** Sử dụng các đường dẫn tạm thời có ký thực (ký bởi Lambda thông qua API Gateway) để tải và hiển thị ảnh, thay thế cho phân phối qua CloudFront do giới hạn xác minh tài khoản AWS.

---

### Các bước thực hành trong Lab
Trong các phần tiếp theo, bạn sẽ tự tay cấu hình các dịch vụ này trên AWS Console:
1. **Điều kiện tiên quyết:** Chuẩn bị tài khoản AWS, mã nguồn dự án, repository GitHub cho frontend.
2. **Cấu hình Storage & Database:** Tạo S3 buckets (raw và processed), tạo 3 bảng DynamoDB (`SmartImage-Images` bật GSI và DynamoDB Stream, `SmartImage-UserQuotas`, và `SmartImage-UserProfiles`).
3. **Xác thực Cognito:** Cấu hình User Pool và App Client để bảo mật các API endpoints.
4. **Backend Serverless:** Tạo các Lambda functions, cấu hình IAM Role, tạo S3 triggers (prefix `users/`) và DynamoDB Stream triggers, cấu hình API Gateway REST API.
5. **Triển khai Frontend:** Kết nối repository GitHub với AWS Amplify để chạy website React thực tế.
6. **Kiểm thử & Xác minh:** Tải ảnh lên và kiểm tra luồng chạy tự động từ S3 -> Lambda -> DynamoDB -> Rekognition.
