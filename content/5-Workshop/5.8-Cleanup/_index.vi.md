---
title: "Dọn dẹp tài nguyên"
date: 2024-01-01
weight: 10
chapter: false
pre: " <b> 5.10. </b> "
---

# Dọn dẹp tài nguyên

Chỉ xóa tài nguyên của đúng tài khoản, Region và environment. Không chạy lệnh destroy production khi chưa kiểm tra dữ liệu cần giữ.

## A. Tài nguyên do CDK quản lý

Từ thư mục gốc `AWS-Project`, xác nhận danh sách stack của `staging`:

```bash
npm run --workspace=infrastructure cdk -- list -c environment=staging
```

Sau đó chạy:

```bash
npm run --workspace=infrastructure destroy -- -c environment=staging
```

1. Đọc danh sách stack CDK sắp xóa và xác nhận chỉ có hậu tố `staging`.
2. Nhập xác nhận khi CDK yêu cầu.
3. Theo dõi CloudFormation Events; nếu một stack `DELETE_FAILED`, mở event đầu tiên có trạng thái failed để tìm tài nguyên phụ thuộc.

Trong `staging`, hai S3 bucket được cấu hình `autoDeleteObjects` và `DESTROY`; CDK có thể làm rỗng/xóa bucket trong quá trình destroy. Không cần làm rỗng thủ công trước trừ khi deployment đã bị thay đổi hoặc custom resource lỗi.

Sau khi destroy, kiểm tra CloudFormation và các dịch vụ liên quan để xác nhận sáu stack đã được xóa. Không xóa thủ công tài nguyên nằm trong stack khi quá trình destroy vẫn đang chạy.

## B. Tài nguyên tạo thủ công trên Console

`cdk destroy` không xóa tài nguyên được tạo riêng trên Console. Xóa theo thứ tự để giảm lỗi dependency.

### 1. Amplify và WAF

1. Mở Amplify Console, chọn app `staging`, chọn **App settings** → **Delete app** và xác nhận theo hướng dẫn.
2. Mở WAF Console, chọn Web ACL trong `ap-southeast-1`.
3. Gỡ association với API Gateway stage trước, sau đó xóa Web ACL. Không xóa Web ACL dùng chung với ứng dụng khác.

### 2. API Gateway và triggers

1. Mở API Gateway, chọn `SmartImage-API-staging`, kiểm tra đúng API ID rồi chọn **Delete**.
2. Mở raw S3 bucket → **Properties** → **Event notifications** và xóa notification gọi ImageProcessor.
3. Mở Lambda AiAnalyzer → **Configuration** → **Triggers** và xóa DynamoDB event source mapping.
4. Xác nhận không còn mapping enabled trước khi xóa function.

### 3. Lambda, logs và queues

1. Trong Lambda Console, xóa ba function nghiệp vụ tạo thủ công. Chỉ xóa provider function có tên dài nếu chắc chắn chúng thuộc stack/lab đang dọn.
2. Trong CloudWatch Logs, xóa log groups của ba function và `/aws/apigateway/SmartImage-staging` nếu không cần giữ log audit.
3. Trong SQS, xóa `SmartImage-ImageProcessorDlq-staging` và `SmartImage-AiAnalyzerDlq-staging` sau khi kiểm tra hoặc lưu các message cần phân tích.

### 4. DynamoDB và S3

1. Mở DynamoDB, chọn lần lượt ba bảng có hậu tố `staging`.
2. Kiểm tra yêu cầu backup/PITR; tạo on-demand backup nếu cần giữ dữ liệu, sau đó chọn **Delete table**.
3. Với processed bucket, chọn **Empty**, nhập xác nhận rồi **Delete bucket**.
4. Với raw bucket có versioning, chọn **Empty** để xóa cả current objects, object versions và delete markers; sau đó mới xóa bucket.
5. Nếu Empty không hoàn tất, bật **Show versions** và kiểm tra version/delete marker còn lại.

### 5. Cognito, monitoring và IAM

1. Trong Cognito, mở đúng User Pool `staging`, kiểm tra không dùng chung với app khác rồi chọn **Delete**. App client và groups sẽ bị xóa cùng pool.
2. Trong CloudWatch, xóa dashboard và alarms có prefix `SmartImage-staging`.
3. Trong SNS, xóa subscriptions rồi topic `SmartImage-Alarms-staging`.
4. Trong IAM, xóa ba role workshop sau khi chúng không còn gắn với Lambda; xóa inline/custom policies chỉ được tạo cho lab.
5. Không xóa `AdministratorAccess`, service-linked role hoặc policy dùng bởi tài nguyên khác.

## C. Kiểm tra sau cleanup

- Kiểm tra lại Region `ap-southeast-1` và các Region khác đã sử dụng.
- Tìm resource có prefix/tag `SmartImage` và environment `staging`.
- Kiểm tra CloudFormation stacks ở trạng thái `DELETE_FAILED`.
- Kiểm tra S3 object versions, CloudWatch Logs, SQS, SNS, WAF và Amplify.
- Theo dõi Billing/Cost Explorer trong những ngày tiếp theo vì dữ liệu chi phí có độ trễ.

Có thể dùng Resource Explorer hoặc Resource Groups Tag Editor để tìm theo `SmartImage`/`staging`, nhưng vẫn cần kiểm tra riêng S3, CloudWatch Logs và tài nguyên retained vì không phải dịch vụ nào cũng xuất hiện ngay trong kết quả tìm kiếm.

> Production dùng `RETAIN` cho một số bucket, bảng và User Pool. `cdk destroy` không xóa các tài nguyên được giữ lại. Không khẳng định chi phí bằng 0 cho đến khi Billing xác nhận và không còn tài nguyên ngoài stack.
