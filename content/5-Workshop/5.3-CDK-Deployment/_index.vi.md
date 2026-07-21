---
title: "Phần I - Triển khai bằng AWS CDK"
date: 2024-01-01
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

# Triển khai Smart Image Platform bằng AWS CDK

Đây là quy trình chính của dự án. Cấu hình trong thư mục `infrastructure/` là nguồn chuẩn khi nội dung Console có khác biệt.

## 1. Cấu trúc stack

| Stack | Tài nguyên chính | Phụ thuộc |
|---|---|---|
| Storage | Raw bucket, processed bucket | Không |
| Database | `Images`, `UserQuotas`, `UserProfiles` | Không |
| Auth | Cognito User Pool, app client, groups | Không |
| API | Lambda, SQS DLQ, API Gateway, S3/DynamoDB triggers, WAF | Storage, Database, Auth |
| Monitoring | Dashboard, alarms, SNS | API |
| Frontend | Amplify app và branch | API, Auth |

CDK thêm tag `Project=SmartImage`, `Environment=<environment>` và `ManagedBy=CDK`. Môi trường mặc định là `staging`; Region được cố định ở `ap-southeast-1`.

## 2. Chuẩn bị biến môi trường

Thiết lập repository, nhánh và credential GitHub trong phiên terminal hiện tại. Không lưu token vào source code:

```powershell
$env:GITHUB_REPO_URL = "https://github.com/<owner>/AWS-Project"
$env:GITHUB_BRANCH = "staging"
$env:GITHUB_TOKEN = "<token-from-secure-storage>"
```

Nếu dùng AWS CLI profile:

```powershell
$env:AWS_PROFILE = "<profile-name>"
aws sts get-caller-identity
```

## 3. Cài dependency và kiểm tra dự án

Chạy tại thư mục gốc `AWS-Project`:

```bash
npm install
npm run build
npm test -- --runInBand
```

CDK dùng `NodejsFunction` và esbuild để bundle TypeScript. `ImageProcessor` cài Sharp dành cho Linux ARM64 trong deployment artifact; không tải trực tiếp thư mục TypeScript hoặc `node_modules` được cài trên Windows lên Lambda.

## 4. Bootstrap và xem thay đổi

Bootstrap chỉ cần thực hiện một lần cho mỗi account/Region:

```bash
npx cdk bootstrap aws://<account-id>/ap-southeast-1
npm run --workspace=infrastructure synth -- -c environment=staging
npm run --workspace=infrastructure cdk -- diff -c environment=staging
```

Kiểm tra template và diff trước khi deploy, đặc biệt các thay đổi IAM, bucket replacement và removal policy.

## 5. Deploy môi trường staging

```bash
npm run --workspace=infrastructure deploy:staging
```

Lệnh deploy tạo sáu stack theo thứ tự phụ thuộc. Sau khi hoàn tất, ghi nhận CloudFormation outputs như API URL, User Pool ID, app client ID, bucket names và Amplify URL.

## 6. Cấu hình quan trọng được CDK tạo

- Raw bucket: private, SSE-S3, versioning, CORS upload và lifecycle transition.
- Processed bucket: private, SSE-S3, không bật versioning, CORS GET/HEAD.
- DynamoDB: On-demand, PITR, stream `NEW_AND_OLD_IMAGES`; hai GSI trên bảng `Images`.
- Lambda: ARM64; `ApiHandler` 512 MB/15 giây, `ImageProcessor` 1536 MB/120 giây/1 GB `/tmp`, `AiAnalyzer` 512 MB/60 giây.
- S3 trigger: `OBJECT_CREATED`, prefix `users/`.
- DynamoDB event source: batch size 10, `TRIM_HORIZON`, retry 3 và SQS on-failure destination.
- API Gateway: các resource/method cụ thể và Cognito User Pool Authorizer.
- Amplify: branch `staging`, bốn biến `VITE_*` và SPA rewrite về `/index.html`.

## 7. Các điểm cần ghi nhận

- Lambda runtime trong mã CDK hiện là Node.js 20.x; cần nâng lên Node.js 22.x hoặc runtime còn được AWS hỗ trợ trước khi triển khai mới.
- `cdk synth/list` hiện cảnh báo deprecation cho `pointInTimeRecovery`, Cognito `advancedSecurityMode` và Lambda `logRetention`. Cần chuyển sang `pointInTimeRecoverySpecification`, threat-protection properties và `logGroup` trong lần cập nhật hạ tầng tiếp theo.
- CloudFront đang bị tắt; ứng dụng trả S3 presigned GET URL.
- Custom Authorizer Lambda vẫn được tạo nhưng không được API Gateway sử dụng; Cognito User Pool Authorizer là cơ chế đang hoạt động.
- Production dùng `RETAIN` cho một số tài nguyên dữ liệu; `cdk destroy` không bảo đảm xóa toàn bộ dữ liệu hoặc đưa chi phí về 0.
