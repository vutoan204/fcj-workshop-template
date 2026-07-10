---
title: "Điều kiện tiên quyết"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

# Điều kiện tiên quyết của Workshop

Trước khi bắt đầu cấu hình hệ thống **Smart Image Platform**, hãy đảm bảo bạn đã chuẩn bị đầy đủ các yếu tố sau:

### 1. Tài khoản AWS (AWS Account)
* Bạn phải có một tài khoản **AWS Account** đang hoạt động.
* IAM User hoặc IAM Role dùng để đăng nhập vào AWS Management Console cần được cấp quyền quản trị (ví dụ gắn policy `AdministratorAccess`).
* Xác định vùng (Region) thực hiện cấu hình (vùng mặc định của dự án là `ap-southeast-1` - Singapore).

![AWS Management Console Login](/images/5-Workshop/5.2-Prerequisites/IAM_Console.png)

### 2. Môi trường phát triển cục bộ (Local Environment)
Cài đặt các công cụ sau trên máy cá nhân nếu bạn muốn build hoặc deploy mã nguồn bằng lệnh:
* **Node.js** (phiên bản 20 trở lên)
* **npm** (phiên bản 10 trở lên)
* **AWS CLI** (đã cấu hình thông tin xác thực tài khoản AWS bằng lệnh `aws configure`)

### 3. Repository GitHub (Cho việc triển khai Frontend CI/CD)
Ứng dụng frontend React sẽ được lưu trữ và triển khai trên AWS Amplify thông qua cơ chế tự động đồng bộ (CI/CD) với GitHub.
* Đẩy mã nguồn dự án của bạn (`AWS-Project`) lên một **GitHub Repository** (ở chế độ public hoặc private).
* Chuẩn bị sẵn một **GitHub Personal Access Token (PAT)** có quyền `repo` và `admin:repo_hook` hoặc thực hiện liên kết xác thực tài khoản GitHub của bạn với dịch vụ AWS Amplify trong quá trình thiết lập.

![GitHub Repository](/images/5-Workshop/5.2-Prerequisites/GitHub_Repo.png)