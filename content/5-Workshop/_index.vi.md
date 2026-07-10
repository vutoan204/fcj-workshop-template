---
title: "Workshop"
date: 2024-01-01
weight: 5
chapter: false
pre: " <b> 5. </b> "
---


# Lab Thiết Lập Smart Image Platform

#### Tổng quan

**Smart Image Platform** là một ứng dụng Serverless và hướng sự kiện (Event-Driven) trên AWS, được thiết kế để tự động hóa việc tải ảnh lên, xử lý ảnh (tạo thumbnail, thay đổi kích thước) và phân loại, gắn nhãn thông minh bằng AI.

Trong bài lab này, bạn sẽ học cách thiết lập và cấu hình toàn bộ hạ tầng của nền tảng **từng bước một bằng tay trên AWS Management Console**. Điều này giúp bạn có cái nhìn chi tiết và trực quan nhất về cách các dịch vụ liên kết với nhau, từ xác thực người dùng, bắt sự kiện S3, chạy Lambda và gọi dịch vụ AI.

#### Nội dung

1. [Tổng quan Workshop](5.1-Workshop-overview/)
2. [Điều kiện tiên quyết](5.2-Prerequisites/)
3. [Cấu hình Storage & Cơ sở dữ liệu](5.3-Storage-Database/)
4. [Xác thực người dùng với Amazon Cognito](5.4-Cognito-Auth/)
5. [Backend Serverless & Kích hoạt Trigger](5.5-Backend-Serverless/)
6. [Triển khai Frontend bằng AWS Amplify](5.6-Frontend-Amplify/)
7. [Kiểm thử & Xác minh luồng hoạt động](5.7-Testing-Validation/)
8. [Dọn dẹp tài nguyên](5.8-Cleanup/)