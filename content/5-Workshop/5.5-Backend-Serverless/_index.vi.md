---
title: "Console - Backend Serverless"
date: 2024-01-01
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

# Backend Serverless trên AWS Console (tùy chọn)

> Đây là bản đồ cấu hình Console, không phải phương thức deploy chính. Nếu API stack đã tồn tại, chỉ dùng Console để quan sát; không tạo lại Lambda, trigger hoặc API cùng tên.

## 1. Execution roles và quyền

### Tạo role thủ công

Lặp lại quy trình dưới đây cho `ApiHandler`, `ImageProcessor` và `AiAnalyzer`:

1. Mở [IAM Console](https://console.aws.amazon.com/iam/) → **Roles** → **Create role**.
2. Chọn **AWS service**, use case **Lambda**, rồi chọn **Next**.
3. Gắn AWS managed policy `AWSLambdaBasicExecutionRole`.
4. Đặt tên lần lượt `SmartImage-ApiHandlerRole-staging`, `SmartImage-ImageProcessorRole-staging` và `SmartImage-AiAnalyzerRole-staging`.
5. Tạo role, mở role vừa tạo và chọn **Add permissions** → **Create inline policy**.
6. Chọn tab **JSON**, thêm các action ở bảng dưới và thay ARN mẫu bằng ARN thật của tài nguyên `staging`.
7. Chọn **Next**, đặt tên policy theo function, rồi chọn **Create policy**.

Mỗi Lambda dùng một execution role riêng, có `AWSLambdaBasicExecutionRole` và các quyền theo tài nguyên `staging`:

| Lambda | Quyền chính |
|---|---|
| `ApiHandler` | S3 Get/Put/Delete; DynamoDB Get/Put/Update/Delete/Query/Scan/BatchGet/BatchWrite trên ba bảng và index; `cognito-idp:AdminUpdateUserAttributes` trên User Pool |
| `ImageProcessor` | S3 Get trên raw bucket, Put trên processed bucket; DynamoDB read/write/query trên bảng `Images`; SQS SendMessage cho DLQ |
| `AiAnalyzer` | S3 Get trên raw bucket; DynamoDB stream read và read/write/batch trên bảng `Images`; Rekognition DetectLabels/DetectModerationLabels; SQS SendMessage cho DLQ |

![Gắn custom policy vào Lambda role trong luồng Console](/images/5-Workshop/5.5-Backend-Serverless/iam_roles_setup.png)

> **Khác với CDK:** CDK tạo role và resource grants tự động. Custom managed policy trong ảnh chỉ là cách cấu hình thủ công tương đương. Resource ARN cần giới hạn theo bucket, table, index, stream, queue và User Pool thực tế.

Khi viết policy JSON, cần bao gồm cả ARN bảng và `table/<name>/index/*` cho thao tác Query trên GSI. Với stream, dùng ARN có dạng `table/<name>/stream/*`; với object S3, ARN kết thúc bằng `/*`. Không dùng `Resource: "*"` cho S3, DynamoDB hoặc Cognito chỉ để làm cho bài lab chạy.

### Tạo hai Dead Letter Queues

1. Mở [Amazon SQS Console](https://console.aws.amazon.com/sqs/) → **Create queue**.
2. Chọn queue type **Standard**.
3. Tạo `SmartImage-ImageProcessorDlq-staging`, giữ encryption mặc định và đặt message retention là 14 ngày.
4. Lặp lại để tạo `SmartImage-AiAnalyzerDlq-staging`.
5. Ghi lại ARN của hai queue để cấu hình quyền `sqs:SendMessage` và on-failure destination.

## 2. Lambda functions

Console walkthrough tập trung vào ba Lambda nghiệp vụ:

| Function | Architecture | Memory | Timeout | Temporary storage |
|---|---:|---:|---:|---:|
| `SmartImage-ApiHandler-staging` | ARM64 | 512 MB | 15 giây | Mặc định |
| `SmartImage-ImageProcessor-staging` | ARM64 | 1536 MB | 120 giây | 1024 MB |
| `SmartImage-AiAnalyzer-staging` | ARM64 | 512 MB | 60 giây | Mặc định |

Ảnh deployment hiện tại hiển thị Node.js 20.x vì đó là runtime trong mã CDK tại thời điểm chụp. Giao diện **Create function** mới hiển thị Node.js 24.x; khi tạo thủ công có thể chọn runtime này sau khi kiểm thử package. Việc chọn runtime mới trên Console không tự cập nhật mã CDK.

![Các Lambda của môi trường staging sau khi deploy CDK](/images/5-Workshop/5.5-Backend-Serverless/lambda_list.png)

Các function có tên dài như `CustomS3AutoDeleteObject`, `BucketNotificationsHandler` và `LogRetention` là provider functions do CDK tạo. `SmartImage-Authorizer-staging` cũng tồn tại nhưng API đang dùng Cognito User Pool Authorizer.

### Tạo function trên Console

Với mỗi Lambda nghiệp vụ:

1. Mở [AWS Lambda Console](https://console.aws.amazon.com/lambda/) → **Functions** → **Create function**.
2. Chọn **Author from scratch**.
3. Trong **Basic information**, nhập đúng **Function name** trong bảng trên.
4. Ở **Runtime**, chọn **Node.js 24.x** hoặc runtime Node.js đang được AWS hỗ trợ và đã được kiểm thử với package.
5. Mở **Additional settings** trong khối **Custom settings**.
6. Bật **ARM64 architecture**.
7. Bật **Custom execution role**. Panel **Configure custom execution role** xuất hiện bên phải.
8. Trong **Execution role**, chọn **Choose an existing role**, chọn role tương ứng rồi nhấn **Save** trong panel.
9. Giữ **Durable execution**, **EC2 capacity provider**, Function URL, VPC và các tùy chọn khác ở trạng thái mặc định vì CDK không dùng chúng.
10. Cuộn xuống cuối trang và chọn **Create function**.

![Tạo Lambda với ARM64 và custom execution role trên giao diện hiện tại](/images/5-Workshop/5.5-Backend-Serverless/lambda_create_function.png)

11. Sau khi function được tạo, mở **Configuration** → **General configuration** → **Edit**, đặt memory và timeout theo bảng.
12. Riêng ImageProcessor, đặt **Ephemeral storage** là `1024 MB`.
13. Mở **Configuration** → **Environment variables** → **Edit** và nhập các biến của function.

### Deployment package

Không zip trực tiếp mã TypeScript. Build bằng esbuild/`NodejsFunction`; riêng `ImageProcessor` phải chứa Sharp dành cho Linux ARM64. AWS SDK v3 có thể được externalize như cấu hình CDK hoặc bundle có kiểm soát.

Để tạo package tương đương CDK:

1. Từ thư mục dự án, cài dependencies và chạy build/test trước khi đóng gói.
2. Bundle từng entry point bằng esbuild cho platform `node`, architecture ARM64 và runtime mục tiêu.
3. Đảm bảo handler được export đúng tên mà Lambda cấu hình (ví dụ `index.handler`).
4. Với ImageProcessor, cài hoặc bundle binary Sharp tương thích `linux-arm64`; package Sharp của Windows không chạy trên Lambda Linux.
5. Nén **nội dung** thư mục output, không nén thêm một thư mục cha.
6. Trong tab **Code**, chọn **Upload from** → **.zip file**, tải package tương ứng và chọn **Save**.
7. Nếu package vượt giới hạn upload trực tiếp, tải lên S3 hoặc dùng Lambda layer/container image; đây là biến thể tùy chọn, không phải cấu hình CDK hiện tại.

### Biến môi trường

Ba Lambda dùng các biến chung: `IMAGE_TABLE_NAME`, `USER_QUOTA_TABLE_NAME`, `USER_PROFILE_TABLE_NAME`, `RAW_BUCKET_NAME`, `PROCESSED_BUCKET_NAME`, `ENVIRONMENT=staging`, `POWERTOOLS_SERVICE_NAME=SmartImage`, `POWERTOOLS_LOG_LEVEL=DEBUG` và `NODE_OPTIONS=--enable-source-maps`.

Bổ sung:

- `ApiHandler`: `USER_POOL_ID`, `PRESIGNED_URL_EXPIRY=900`.
- `ImageProcessor`: `THUMBNAIL_WIDTH=200`, `THUMBNAIL_HEIGHT=200`, `RESIZED_MAX_WIDTH=1920`, `RESIZED_MAX_HEIGHT=1080`.
- `AiAnalyzer`: bắt buộc có `RAW_BUCKET_NAME` và `IMAGE_TABLE_NAME` để đọc ảnh và cập nhật metadata.

Nhập tên vật lý thật của bucket/table/User Pool thay cho logical name. Sau khi lưu, mở **Configuration** → **Permissions** để xác nhận execution role đúng; sau đó chạy một test event nhỏ hoặc mở log group để chắc chắn function khởi tạo được trước khi gắn trigger.

![Các biến môi trường thực tế của ImageProcessor staging](/images/5-Workshop/5.5-Backend-Serverless/lambda_environment_variables.png)

Ảnh minh họa ImageProcessor có 15 biến. `AI_ANALYZER_FUNCTION_NAME` đang để trống theo đúng CDK hiện tại vì pipeline sử dụng DynamoDB Stream để gọi AiAnalyzer, không invoke trực tiếp từ ImageProcessor.

## 3. S3 Event Notification

1. Mở raw bucket trong Amazon S3 Console.
2. Chọn tab **Properties** và cuộn đến **Event notifications**.
3. Chọn **Create event notification**.
4. Nhập tên `InvokeImageProcessor-staging`.
5. Nhập prefix `users/`; để suffix trống.
6. Ở **Event types**, chọn **All object create events**.
7. Ở **Destination**, chọn **Lambda function** và chọn `SmartImage-ImageProcessor-staging`.
8. Chọn **Save changes**. S3 sẽ thêm resource-based permission để được gọi Lambda.

Cấu hình cần đạt:

- Event: **All object create events**.
- Prefix: `users/`.
- Destination: `SmartImage-ImageProcessor-staging`.
- Không thêm suffix nếu muốn để Lambda thực hiện validation định dạng.

![Destination của S3 Event Notification](/images/5-Workshop/5.5-Backend-Serverless/s3_trigger_setup.png)

Ảnh chỉ minh họa destination; event type và prefix phải được nhập theo danh sách trên.

## 4. DynamoDB Stream event source

1. Xác nhận stream của `SmartImage-Images-staging` đã bật với **New and old images**.
2. Mở DynamoDB table → **Exports and streams** → **DynamoDB stream details**.
3. Chọn **Create trigger** hoặc mở Lambda `SmartImage-AiAnalyzer-staging` → **Add trigger** → **DynamoDB**.
4. Chọn stream ARN của bảng Images.
5. Đặt **Batch size** là `10` và bật trigger.
6. Chọn **Add/Create trigger**.

![Tạo DynamoDB trigger với batch size 10](/images/5-Workshop/5.5-Backend-Serverless/dynamodb_trigger_setup.png)

Sau khi tạo, mở event source mapping trong Lambda để kiểm tra/cấu hình:

- Starting position: `TRIM_HORIZON`.
- Maximum retry attempts: `3`.
- On-failure destination: SQS queue `SmartImage-AiAnalyzerDlq-staging`.
- Trigger enabled.

Ảnh chỉ thể hiện form tạo trigger ban đầu; retry và destination được CDK khai báo thêm.

Nếu Console không hiển thị retry và destination trong form DynamoDB, mở Lambda → **Configuration** → **Triggers**, chọn event source mapping rồi **Edit**. Cấp quyền đọc stream cho role trước khi bật mapping; nếu không, trạng thái trigger sẽ báo lỗi.

## 5. API Gateway và Cognito Authorizer

### Tạo REST API

1. Mở [API Gateway Console](https://console.aws.amazon.com/apigateway/) → **Create API**.
2. Ở **REST API**, chọn **Build**. Không chọn HTTP API vì CDK dùng REST API v1.
3. Chọn **New API**, nhập tên `SmartImage-API-staging`, endpoint type **Regional**, rồi chọn **Create API**.

### Tạo Cognito authorizer

1. Trong API vừa tạo, chọn **Authorizers** → **Create authorizer**.
2. Nhập tên `CognitoAuth`, type **Cognito**.
3. Chọn Region `ap-southeast-1` và User Pool `SmartImage-UserPool-staging`.
4. Ở **Token source**, nhập `Authorization`; để token validation trống.
5. Chọn **Create authorizer**.

![Cognito User Pool Authorizer trên API Gateway](/images/5-Workshop/5.5-Backend-Serverless/api_gateway_authorizer.png)

### Tạo resources và methods

1. Chọn **Resources** → **Create resource**, bắt đầu với resource `/v1`.
2. Tạo các resource con theo đúng cây đường dẫn. Với `{imageId}`, giữ dấu ngoặc nhọn để API Gateway nhận đây là path parameter.
3. Chọn resource cần gắn method rồi chọn **Create method**.
4. Trong **Method details**, chọn **Method type** tương ứng như `GET`, `POST`, `PATCH` hoặc `DELETE`.
5. Ở **Integration type**, chọn **Lambda function**.
6. Bật **Lambda proxy integration** để chuyển request thành event có cấu trúc cho ApiHandler.
7. Ở **Response transfer mode**, chọn **Buffered**; dự án không dùng response streaming.
8. Ở **Lambda function**, chọn Region `ap-southeast-1`, tìm và chọn `SmartImage-ApiHandler-staging`.
9. Giữ default timeout nếu không có yêu cầu khác. Khi lưu, chấp nhận để API Gateway thêm permission invoke vào resource-based policy của Lambda.
10. Chọn **Create method**.

![Tạo REST API method với Lambda proxy integration](/images/5-Workshop/5.5-Backend-Serverless/api_gateway_create_method.png)

11. Mở method vừa tạo → tab **Method request** → **Edit**.
12. Với route protected, chọn **Authorization/Authorizer: CognitoAuth**. Với `GET /v1/images/public`, giữ **None**.
13. Với `PATCH`/`POST`, chọn request validator tương đương CDK; sau đó lưu method request.
14. Lặp lại theo bảng sau:

| Method | Path | Authorization |
|---|---|---|
| `GET`, `PATCH` | `/v1/profile` | `CognitoAuth` |
| `POST` | `/v1/profile/avatar/presigned-url` | `CognitoAuth` |
| `GET` | `/v1/images` | `CognitoAuth` |
| `POST` | `/v1/images/presigned-url` | `CognitoAuth` |
| `GET` | `/v1/images/public` | `NONE` |
| `DELETE` | `/v1/images/bulk` | `CognitoAuth` |
| `GET` | `/v1/images/search` | `CognitoAuth` |
| `GET`, `DELETE`, `PATCH` | `/v1/images/{imageId}` | `CognitoAuth` |
| `GET` | `/v1/images/{imageId}/download` | `CognitoAuth` |
| `GET` | `/v1/admin/moderation` | `CognitoAuth` và kiểm tra group trong Lambda |
| `POST` | `/v1/admin/moderation/{imageId}` | `CognitoAuth` và kiểm tra group trong Lambda |

15. Bật CORS trên các resource được frontend gọi; cho phép origin Amplify, headers `Content-Type,Authorization` và các methods thực tế.
16. Chọn **Deploy API**, tạo stage name `dev`, rồi deploy.
17. Sao chép invoke URL có dạng `https://<api-id>.execute-api.ap-southeast-1.amazonaws.com/dev`.
18. Gọi `GET /v1/images/public` không token để kiểm tra route public; gọi route protected không token phải nhận `401 Unauthorized`.

Trong CDK hiện tại, environment `staging` được ánh xạ sang API Gateway stage `dev`; production dùng stage `prod`.

> CDK hiện còn khai báo `/v1/auth/signup`, `/login` và `/refresh`, nhưng router Lambda không triển khai các handler tương ứng; frontend xác thực trực tiếp với Cognito. Không dùng ba route này trong Console walkthrough.

## 6. AWS WAF Web ACL (tùy chọn trên Console)

CDK tạo một AWS WAF v2 Web ACL Regional và gắn trực tiếp với API Gateway stage `dev`. WAF được đánh giá trước Cognito authorizer, vì vậy request bị WAF chặn sẽ không đi đến bước xác thực hoặc Lambda.

> AWS WAF có chi phí riêng cho Web ACL, rule và request. Có thể chỉ quan sát tài nguyên do CDK tạo nếu không muốn phát sinh thêm tài nguyên thủ công.

### Tạo Web ACL

Trên giao diện mới, AWS gọi Web ACL là **Protection pack (web ACL)**. Đây chỉ là thay đổi tên hiển thị; CDK và API vẫn dùng khái niệm `WebACL`.

1. Mở [AWS WAF Console](https://console.aws.amazon.com/wafv2/homev2) → **Protection packs (web ACLs)**.
2. Kiểm tra **Region scope** và chọn Region `ap-southeast-1`, sau đó chọn **Create protection pack (web ACL)**.

![Danh sách Protection packs (web ACLs) trên giao diện WAF mới](/images/5-Workshop/5.5-Backend-Serverless/waf_protection_packs.png)

3. Trong **Tell us about your app**, chọn app category **API & integration services**. Có thể chọn thêm **Media & file processing**, nhưng đây chỉ là thông tin để Console đề xuất protection và không có trong CDK.
4. Mở **Select resources to protect**, chọn **add resources** -> **Add regional resources**, API `SmartImage-API-staging` và stage `dev`.
5. Trong **Choose initial protections**, chỉ giữ hoặc bổ sung các rule cần thiết để tương đương CDK; các protection package khác do Console đề xuất là tùy chọn.
6. Mở **Name and describe**, nhập tên `SmartImage-ApiWebAcl-staging` và CloudWatch metric name `SmartImage-WafMetrics-staging`.
7. Giữ web request body inspection size mặc định và default action **Allow**; CDK không tăng giới hạn inspection body.
8. Tiếp tục đến trang review và tạo protection pack.

![Wizard Create protection pack với app category và resource selection](/images/5-Workshop/5.5-Backend-Serverless/waf_create_protection_pack.png)

### Thêm AWS Managed Rules

1. Chọn **Add rules** → **Add managed rule groups**.
2. Mở **AWS managed rule groups** và bật `Core rule set (AWSManagedRulesCommonRuleSet)`.
3. Giữ rule action theo rule group (**Use rule actions/None**) để khớp `overrideAction: none` trong CDK.
4. Lưu rule với priority `1` và bật CloudWatch metrics/sampled requests.
5. Khi triển khai ngoài lab, có thể đặt managed rule group ở chế độ Count trước để quan sát false positive rồi mới chuyển sang action thật; đây là lựa chọn vận hành khác CDK hiện tại.

### Thêm rate-based rule

1. Chọn **Add rules** → **Add my own rules and rule groups** → **Rule builder**.
2. Chọn rule type **Rate-based rule** và nhập tên `RateLimitRule`.
3. Chọn aggregate theo **Source IP address**.
4. Nhập rate limit `2000`, evaluation window **5 minutes** (mặc định khi CDK không khai báo giá trị) và chọn action **Block**.
5. Đặt priority `2`, metric name `RateLimitRuleMetric`, bật CloudWatch metrics và sampled requests.
6. Giữ Web ACL default action là **Allow** rồi chọn **Create web ACL**.

### Kiểm tra association

1. Mở API Gateway → `SmartImage-API-staging` → **Stages** → `dev` → **Edit**.
2. Trong **Web application firewall (AWS WAF)**, chọn Web ACL vừa tạo và lưu.
3. Quay lại WAF → Web ACL → **Associated AWS resources** để xác nhận stage `dev` xuất hiện.
4. Mở **Sampled requests** và CloudWatch metric của hai rules sau khi API có traffic.

<!-- Chèn ảnh WAF rules/association tại đây khi có: /images/5-Workshop/5.5-Backend-Serverless/waf_rules_association.png -->

## 7. CloudWatch, alarms và SNS (tùy chọn trên Console)

### API Gateway access logs

1. Mở CloudWatch → **Logs** → **Log management** → **Create log group**.
2. Nhập `/aws/apigateway/SmartImage-staging`, chọn retention 30 ngày và log class **Standard**.
3. Để KMS key trống nếu dùng service-managed encryption; deletion protection là tùy chọn và CDK hiện không bật.

![Tạo CloudWatch log group với retention và log class](/images/5-Workshop/5.5-Backend-Serverless/cloudwatch_create_log_group.png)

4. Mở API Gateway → API → **Stages** → `dev` → **Logs and tracing/Edit**.
5. Bật access logging, chọn ARN của log group và dùng JSON access-log format chứa các standard fields như request ID, IP, caller, HTTP method, resource path, status, protocol, response length và request time.
6. CDK cũng bật X-Ray tracing cho stage. Bật **X-Ray tracing** nếu muốn cấu hình Console tương đương.

### SNS topic và email subscription

1. Mở [Amazon SNS Console](https://console.aws.amazon.com/sns/) → **Topics** → **Create topic**.
2. Chọn type **Standard**, name `SmartImage-Alarms-staging`, display name `SmartImage staging Alarms`.
3. Giữ Encryption, Access policy và Delivery policy mặc định để khớp cấu hình lab, rồi chọn **Create topic**.

![Tạo SNS Standard topic cho cảnh báo staging](/images/5-Workshop/5.5-Backend-Serverless/sns_create_topic.png)

4. Trong topic vừa tạo, chọn **Create subscription**, protocol **Email** và nhập địa chỉ nhận cảnh báo.
5. Mở email từ AWS Notification và chọn **Confirm subscription**. Trạng thái `Pending confirmation` không nhận alarm notification.

### Tạo các alarms tương đương CDK

1. Trong CloudWatch chọn **Alarms** → **All alarms** → **Create alarm**.
2. Ở Step 1 **Specify metric and conditions**, chọn data source **Metrics**, type **Classic**, rồi chọn **Select metric**.

![Bước chọn Metrics và Classic khi tạo CloudWatch alarm](/images/5-Workshop/5.5-Backend-Serverless/cloudwatch_create_alarm.png)

3. Trong cửa sổ metric, chọn namespace Lambda, ApiGateway hoặc DynamoDB và dimension trong bảng dưới.
4. Thiết lập statistic, period, threshold và missing data.
5. Ở Step 2 **Configure actions**, chọn **In alarm** → gửi notification đến `SmartImage-Alarms-staging`.
6. Ở Step 3, đặt tên có prefix `SmartImage-staging-`; xem lại ở Step 4 rồi chọn **Create alarm**.

| Nhóm | Metric và dimension | Statistic / Period | Điều kiện |
|---|---|---|---|
| Mỗi Lambda | `AWS/Lambda` → `Errors`, `FunctionName` | Sum / 5 phút | `> 5`, 1 period |
| Mỗi Lambda | `AWS/Lambda` → `Duration`, `FunctionName` | p95 / 5 phút | ApiHandler `>12000 ms`; ImageProcessor `>96000 ms`; AiAnalyzer `>48000 ms`, 2 periods |
| Mỗi Lambda | `AWS/Lambda` → `Throttles`, `FunctionName` | Sum / 5 phút | `>=1`, 1 period |
| API Gateway | `AWS/ApiGateway` → `5XXError`, `ApiName` | Sum / 5 phút | `>10`, 1 period |
| API Gateway | `AWS/ApiGateway` → `Latency`, `ApiName` | p95 / 5 phút | `>3000 ms`, 2 periods |
| DynamoDB Images | `AWS/DynamoDB` → `ThrottledRequests`, `TableName` | Sum / 5 phút | `>=1`, 1 period |

Đặt missing data là **Treat missing data as not breaching**. Tên alarm dùng prefix `SmartImage-staging-`; alarm Errors chỉ chuyển ALARM khi có ít nhất 6 lỗi trong một period vì toán tử là **Greater than 5**.

### Tạo operational dashboard

1. Chọn **Dashboards** → **Create dashboard**, nhập `SmartImage-staging-Operations`.
2. Thêm text widget làm tiêu đề.
3. Với mỗi Lambda, thêm graph **Invocations & Errors**: Invocations/Sum ở trục trái, Errors/Sum ở trục phải, period 5 phút.
4. Với mỗi Lambda, thêm graph **Duration (ms)** gồm Average và p95, period 5 phút.
5. Sắp xếp ba graph mỗi hàng và chọn **Save dashboard**.
6. Dashboard là global trong tài khoản nhưng metric widget vẫn đọc dữ liệu theo Region; kiểm tra metric đang lấy từ `ap-southeast-1`.

<!-- Chèn ảnh CloudWatch dashboard tại đây khi có: /images/5-Workshop/5.5-Backend-Serverless/cloudwatch_dashboard_setup.png -->

Các bước WAF và CloudWatch là phần minh họa Console. Nếu stack CDK đã deploy, chỉ quan sát và đối chiếu các tài nguyên hiện có; không tạo bản sao cùng chức năng.
