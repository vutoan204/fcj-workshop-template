---
title: "Điều kiện tiên quyết"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

# Điều kiện tiên quyết

## 1. Tài khoản và quyền AWS

- Không sử dụng root user cho các thao tác hằng ngày.
- Ưu tiên IAM Identity Center hoặc IAM role. Đối với tài khoản sandbox cá nhân, có thể dùng IAM user được bật MFA.
- `AdministratorAccess` giúp đơn giản hóa workshop nhưng chỉ phù hợp với sandbox. Môi trường thực tế cần permission set hoặc policy theo nguyên tắc least privilege.
- Region sử dụng trong workshop: `ap-southeast-1` (Singapore).

![IAM user dùng trong tài khoản sandbox](/images/5-Workshop/5.2-Prerequisites/IAM_Console.png)

> Ảnh minh họa một IAM user trong môi trường sandbox. Không áp dụng cách gắn `AdministratorAccess` trực tiếp cho workload production.

## 2. Môi trường phát triển

Cài đặt và kiểm tra:

```bash
node --version
npm --version
aws --version
cdk --version
git --version
```

Yêu cầu dự án hiện tại:

- Node.js 20 trở lên để build workspace; Lambda runtime trong mã CDK hiện là Node.js 20.x và cần được nâng lên runtime còn được hỗ trợ trước khi triển khai dài hạn.
- AWS CLI đã cấu hình profile hoặc thông tin đăng nhập tạm thời.
- AWS CDK CLI v2.
- Git và quyền đọc repository `AWS-Project`.

Kiểm tra danh tính AWS trước khi deploy:

```bash
aws sts get-caller-identity
```

## 3. Repository và Amplify

Amplify Console sử dụng GitHub App để kết nối repository. Chọn đúng repository và nhánh `staging` hoặc `main`, khuyên dùng `staging`.

CDK hiện nhận các giá trị `GITHUB_REPO_URL`, `GITHUB_BRANCH` và `GITHUB_TOKEN` từ biến môi trường để tạo Amplify app. Token không được commit vào Git hoặc ghi trực tiếp vào tài liệu.

## 4. Chi phí và phạm vi

- Dùng môi trường `staging` cho workshop.
- Theo dõi Billing/Budgets trong suốt quá trình.
- WAF, NAT Gateway, log storage, Rekognition và các tài nguyên chạy lâu có thể phát sinh chi phí ngoài Free Tier.
- Không deploy `production` nếu chưa kiểm tra removal policy `RETAIN`.
