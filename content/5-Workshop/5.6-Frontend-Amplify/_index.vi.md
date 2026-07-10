---
title: "Triển khai Frontend bằng AWS Amplify"
date: 2024-01-01
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

# Triển khai React Frontend bằng AWS Amplify (AWS Console)

Trong phần này, bạn sẽ thực hiện đưa ứng dụng giao diện React Client lên môi trường internet thông qua **AWS Amplify**, kết nối tự động triển khai (CI/CD) từ GitHub và cấu hình các biến môi trường phía client.

---

### Bước 1: Kết nối GitHub Repository với AWS Amplify

1. Mở [AWS Amplify Console](https://console.aws.amazon.com/amplify/).
2. Chọn **Create new app** (hoặc chọn **Get Started** dưới mục **Amplify Hosting**).
3. Chọn **GitHub** làm nhà cung cấp mã nguồn và nhấn **Next** (Thực hiện ủy quyền kết nối Amplify với tài khoản GitHub của bạn nếu được yêu cầu).
4. **Repository:** Tìm chọn repository chứa dự án `AWS-Project` của bạn trên GitHub.
5. **Branch:** Chọn nhánh code muốn deploy (ví dụ: `main`, `staging`, hoặc nhánh tính năng của bạn).
6. Nhấn **Next**.

![Cấu hình AWS Amplify App](/images/5-Workshop/5.6-Frontend-Amplify/amplify_app_setup.png)

---

### Bước 2: Cấu hình Build Settings & Biến môi trường

AWS Amplify cần biết cách đóng gói (build) ứng dụng React và địa chỉ API Gateway để gửi request.

#### A. Cấu hình các biến môi trường (Environment Variables)
1. Tại trang cấu hình **App settings**, mở rộng mục **Advanced settings** (hoặc mục **Environment variables**).
2. Thêm các biến key-value sau đây (sử dụng các giá trị User Pool ID và API Gateway Invoke URL bạn đã lưu ở các bước trước):
   * `VITE_API_URL` = `https://<api-id>.execute-api.ap-southeast-1.amazonaws.com/dev` *(Lưu ý: Không để dấu gạch chéo / ở cuối URL)*
   * `VITE_USER_POOL_ID` = `<user-pool-id-cua-ban>` (ví dụ: `ap-southeast-1_xxxxxx`)
   * `VITE_CLIENT_ID` = `<app-client-id-cua-ban>` (ví dụ: `7xxxxxxxxxxxxxxxxxxxxxxxxx`)
   * `VITE_AWS_REGION` = `ap-southeast-1`

![Cấu hình Amplify Environment Variables](/images/5-Workshop/5.6-Frontend-Amplify/amplify_env_setup.png)

#### B. Thiết lập cấu hình Build (`amplify.yml`)
Do dự án này sử dụng mô hình monorepo (npm workspaces), ta cần cấu hình cụ thể lệnh build và thư mục đích cho Amplify:
1. Tại khung cấu hình build settings, dán nội dung cấu hình `amplify.yml` tùy biến sau:
   ```yaml
   version: 1
   frontend:
     phases:
       preBuild:
         commands:
           - npm ci
       build:
         commands:
           - npm run --workspace=frontend build
     artifacts:
       baseDirectory: frontend/dist
       files:
         - '**/*'
     cache:
       paths:
         - node_modules/**/*
   ```
2. Nhấn **Next**.

---

### Bước 3: Kiểm tra và Triển khai

1. Xem lại toàn bộ thông tin về repository, nhánh deploy, biến môi trường và file cấu hình build.
2. Chọn **Save and deploy**.
3. AWS Amplify sẽ tự động thực hiện các bước khởi tạo môi trường (Provision), tải code & đóng gói (Build) và đưa lên hosting (Deploy). Quá trình này diễn ra trong khoảng từ 3 đến 5 phút.
4. Sau khi hoàn tất, Amplify sẽ cung cấp một đường dẫn tên miền mặc định **Domain URL** (ví dụ: `https://main.xxxxxxxx.amplifyapp.com`). Bạn có thể bấm vào link này để mở giao diện web React trực tiếp.
