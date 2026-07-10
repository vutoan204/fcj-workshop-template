---
title: "Dọn dẹp tài nguyên"
date: 2024-01-01
weight: 8
chapter: false
pre: " <b> 5.8. </b> "
---


# Dọn dẹp tài nguyên sau khi kết thúc Lab

Để tránh phát sinh các chi phí không mong muốn trên tài khoản AWS của bạn sau khi hoàn thành bài thực hành, hãy thực hiện dọn dẹp các tài nguyên đã khởi tạo theo các phương pháp dưới đây.

---

### Phương pháp A: Dọn dẹp thủ công bằng AWS Management Console

Thực hiện xóa các tài nguyên theo thứ tự ngược lại so với lúc tạo để tránh lỗi ràng buộc phụ thuộc (dependency barriers).

#### Bước 1: Xóa ứng dụng AWS Amplify
1. Mở [AWS Amplify Console](https://console.aws.amazon.com/amplify/).
2. Chọn ứng dụng frontend của bạn (ví dụ `smart-image-frontend` hoặc tên project tương ứng).
3. Chọn **Actions** -> **Delete app**.
4. Nhập chữ `delete` để xác nhận xóa, sau đó chọn **Delete**.

#### Bước 2: Xóa Amazon API Gateway
1. Mở [API Gateway Console](https://console.aws.amazon.com/apigateway/).
2. Tìm kiếm API tên là `SmartImage-API` trong danh sách APIs của bạn.
3. Chọn API, chọn **Actions** -> **Delete**.
4. Xác nhận xóa.

#### Bước 3: Xóa các hàm AWS Lambda
1. Mở [AWS Lambda Console](https://console.aws.amazon.com/lambda/).
2. Tìm kiếm 3 hàm sau trong danh sách:
   * `SmartImage-ApiHandler`
   * `SmartImage-ImageProcessor`
   * `SmartImage-AIAnalyzer`
3. Tích chọn các hàm này, click **Actions** -> **Delete**.
4. Xác nhận xóa.

#### Bước 4: Xóa Amazon DynamoDB Tables
1. Mở [Amazon DynamoDB Console](https://console.aws.amazon.com/dynamodb/).
2. Bấm chọn mục **Tables** ở thanh menu bên trái.
3. Tích chọn các bảng `SmartImage-Images`, `SmartImage-UserQuotas`, và `SmartImage-UserProfiles`.
4. Chọn **Delete**.
5. Nhập chữ `delete` để xác nhận xóa.

#### Bước 5: Xóa các Amazon S3 Buckets
*Lưu ý: S3 bucket cần được làm rỗng dữ liệu (Empty) trước khi có thể xóa hoàn toàn.*
1. Mở [Amazon S3 Console](https://s3.console.aws.amazon.com/).
2. Chọn bucket thô `smartimage-raw-bucket-<ten-cua-ban>`.
3. Chọn nút **Empty** và thực hiện theo hướng dẫn hiển thị để xóa toàn bộ các đối tượng bên trong bucket.
4. Sau khi bucket đã rỗng, quay lại chọn bucket đó, nhấn **Delete**, nhập tên bucket để xác nhận xóa hoàn toàn.
5. Thực hiện các bước tương tự đối với bucket kết quả `smartimage-processed-bucket-<ten-cua-ban>`.

#### Bước 6: Xóa Amazon Cognito User Pool
1. Mở [Amazon Cognito Console](https://console.aws.amazon.com/cognito/).
2. Chọn User Pool `SmartImage-UserPool`.
3. Bấm **Delete**.
4. Điền tên User Pool để xác nhận và thực hiện xóa.

#### Bước 7: Xóa các IAM Roles & Policies
1. Mở [IAM Console](https://console.aws.amazon.com/iam/).
2. Chọn mục **Roles**, tìm kiếm từ khóa `SmartImage-` để hiển thị các Lambda execution roles đã tạo, tích chọn và bấm **Delete**.
3. Chọn mục **Policies**, tìm kiếm từ khóa `SmartImage-`, chọn các policy tùy chỉnh tương ứng và bấm **Delete**.

#### Bước 8: Xóa Dashboards, Alarms & SNS Topics của CloudWatch
1. Mở [Amazon CloudWatch Console](https://console.aws.amazon.com/cloudwatch/).
2. Bấm vào **Dashboards** ở menu trái, tích chọn dashboard `SmartImage-dev-Operations`, và bấm **Delete**.
3. Chọn **Alarms** -> **All alarms**, tích chọn các cảnh báo bắt đầu bằng `SmartImage-dev-` và bấm **Delete**.
4. Mở [Amazon SNS Console](https://console.aws.amazon.com/sns/).
5. Bấm vào **Topics**, chọn topic `SmartImage-Alarms-dev` và bấm **Delete**.

---

### Phương pháp B: Xóa tự động bằng Infrastructure-as-Code (AWS CDK)

Nếu bạn đã triển khai hệ thống thông qua bộ công cụ phát triển phần mềm AWS CDK:
1. **Làm rỗng S3 Buckets:** Bạn vẫn phải vào S3 Console để làm rỗng (Empty) hai bucket raw và processed bằng tay trước (vì AWS không cho phép CloudFormation tự động xóa các bucket đang có chứa file trừ khi được cấu hình force-delete đặc biệt).
2. **Chạy lệnh CDK Destroy:** Tại terminal của môi trường máy phát triển local, di chuyển tới thư mục gốc dự án CDK và chạy lệnh:
   ```bash
   cdk destroy --all
   ```
3. Nhập `y` (yes) để xác nhận. Lệnh này sẽ tự động thu hồi và xóa sạch API Gateway, Lambda, Cognito User Pool, IAM roles/policies, hàng đợi SQS DLQ, cùng toàn bộ Dashboards và Alarms chỉ trong một lệnh duy nhất.
