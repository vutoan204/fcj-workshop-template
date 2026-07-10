---
title: "Serverless Backend & Triggers"
date: 2024-01-01
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---


# Serverless Backend & Triggers (AWS Console)

Trong phần này, bạn sẽ tạo các IAM Policy và Role trước, sau đó triển khai mã nguồn lên Lambda, thiết lập các bộ kích hoạt tự động và cấu hình Amazon API Gateway.

---

### Bước 1: Tạo các chính sách IAM tùy chỉnh (Custom IAM Policies)

Trước khi tạo các vai trò thực thi (execution roles) cho Lambda, chúng ta cần tạo các chính sách phân quyền (permission policies) tùy chỉnh trước trong IAM.

#### A. Tạo chính sách cho API Handler (`SmartImage-ApiHandler-Policy`)
1. Mở [IAM Console](https://console.aws.amazon.com/iam/).
2. Tại thanh menu bên trái, dưới mục **Access Management**, chọn **Policies**.
3. Chọn nút màu cam **Create policy**.
4. Tại mục **Policy editor**, chuyển sang tab **JSON** (ngay cạnh tab Visual). Xóa toàn bộ mã mẫu mặc định và dán nội dung chính sách sau:
   ```json
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Action": [
           "dynamodb:GetItem",
           "dynamodb:PutItem",
           "dynamodb:UpdateItem",
           "dynamodb:DeleteItem",
           "dynamodb:Query",
           "dynamodb:Scan"
         ],
         "Resource": [
           "arn:aws:dynamodb:ap-southeast-1:*:table/SmartImage-Images*",
           "arn:aws:dynamodb:ap-southeast-1:*:table/SmartImage-UserQuotas*",
           "arn:aws:dynamodb:ap-southeast-1:*:table/SmartImage-UserProfiles*"
         ]
       },
       {
         "Effect": "Allow",
         "Action": [
           "s3:PutObject",
           "s3:GetObject"
         ],
         "Resource": [
           "arn:aws:s3:::smartimage-raw-bucket-*/*",
           "arn:aws:s3:::smartimage-processed-bucket-*/*"
         ]
       },
       {
         "Effect": "Allow",
         "Action": [
           "cognito-idp:AdminUpdateUserAttributes"
         ],
         "Resource": "arn:aws:cognito-idp:ap-southeast-1:*:userpool/*"
       },
       {
         "Effect": "Allow",
         "Action": [
           "logs:CreateLogGroup",
           "logs:CreateLogStream",
           "logs:PutLogEvents"
         ],
         "Resource": "arn:aws:logs:*:*:*"
       }
     ]
   }
   ```
5. Chọn **Next**.
6. **Policy name:** Điền `SmartImage-ApiHandler-Policy`.
7. Chọn **Create policy**.

#### B. Tạo chính sách cho Image Processor (`SmartImage-ImageProcessor-Policy`)
1. Bấm vào **Policies** ở menu trái, sau đó chọn **Create policy**.
2. Chuyển sang trình soạn thảo **JSON** và dán nội dung sau:
   ```json
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Action": [
           "s3:GetObject"
         ],
         "Resource": "arn:aws:s3:::smartimage-raw-bucket-*/*"
       },
       {
         "Effect": "Allow",
         "Action": [
           "s3:PutObject"
         ],
         "Resource": "arn:aws:s3:::smartimage-processed-bucket-*/*"
       },
       {
         "Effect": "Allow",
         "Action": [
           "dynamodb:UpdateItem",
           "dynamodb:GetItem"
         ],
         "Resource": "arn:aws:dynamodb:ap-southeast-1:*:table/SmartImage-Images*"
       },
       {
         "Effect": "Allow",
         "Action": [
           "logs:CreateLogGroup",
           "logs:CreateLogStream",
           "logs:PutLogEvents"
         ],
         "Resource": "arn:aws:logs:*:*:*"
       }
     ]
   }
   ```
3. Chọn **Next**, đặt tên chính sách là `SmartImage-ImageProcessor-Policy`, rồi nhấn **Create policy**.

#### C. Tạo chính sách cho AI Analyzer (`SmartImage-AIAnalyzer-Policy`)
1. Bấm vào **Policies** ở menu trái, chọn **Create policy**.
2. Chuyển sang trình soạn thảo **JSON** và dán nội dung sau:
   ```json
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Action": [
           "dynamodb:GetRecords",
           "dynamodb:GetShardIterator",
           "dynamodb:DescribeStream",
           "dynamodb:ListStreams"
         ],
         "Resource": "arn:aws:dynamodb:ap-southeast-1:*:table/SmartImage-Images*/stream/*"
       },
       {
         "Effect": "Allow",
         "Action": [
           "rekognition:DetectLabels",
           "rekognition:DetectModerationLabels"
         ],
         "Resource": "*"
       },
       {
         "Effect": "Allow",
         "Action": [
           "s3:GetObject"
         ],
         "Resource": "arn:aws:s3:::smartimage-raw-bucket-*/*"
       },
       {
         "Effect": "Allow",
         "Action": [
           "dynamodb:UpdateItem"
         ],
         "Resource": "arn:aws:dynamodb:ap-southeast-1:*:table/SmartImage-Images*"
       },
       {
         "Effect": "Allow",
         "Action": [
           "logs:CreateLogGroup",
           "logs:CreateLogStream",
           "logs:PutLogEvents"
         ],
         "Resource": "arn:aws:logs:*:*:*"
       }
     ]
   }
   ```
3. Chọn **Next**, đặt tên chính sách là `SmartImage-AIAnalyzer-Policy`, rồi chọn **Create policy**.

---

### Bước 2: Tạo các IAM Roles cho Lambda Functions

Sau khi đã tạo xong các chính sách phân quyền ở Bước 1, chúng ta tiến hành tạo các vai trò thực thi và gán chính sách tương ứng vào.

#### A. Tạo `SmartImage-ApiHandler-Role`
1. Tại thanh menu bên trái dưới mục **Access Management**, chọn **Roles**.
2. Chọn **Create role**.
3. **Step 1: Select trusted entity:** Chọn **AWS service** và chọn use case là **Lambda**. Nhấn **Next**.
4. **Step 2: Add permissions:**
   * Tại ô tìm kiếm Search, gõ tìm tên chính sách `SmartImage-ApiHandler-Policy`.
   * Tích chọn ô vuông bên cạnh chính sách đó trong danh sách. Chọn **Next**.
5. **Step 3: Name, review, and create:**
   * **Role name:** Điền tên role là `SmartImage-ApiHandler-Role`.
   * Chọn **Create role**.

#### B. Tạo `SmartImage-ImageProcessor-Role`
1. Chọn **Roles** -> **Create role**. Chọn **Lambda** và nhấn **Next**.
2. Tìm kiếm và tích chọn chính sách `SmartImage-ImageProcessor-Policy`. Nhấn **Next**.
3. **Role name:** Điền `SmartImage-ImageProcessor-Role`. Chọn **Create role**.

#### C. Tạo `SmartImage-AIAnalyzer-Role`
1. Chọn **Roles** -> **Create role**. Chọn **Lambda** và nhấn **Next**.
2. Tìm kiếm và tích chọn chính sách `SmartImage-AIAnalyzer-Policy`. Nhấn **Next**.
3. **Role name:** Điền `SmartImage-AIAnalyzer-Role`. Chọn **Create role**.

![Cấu hình IAM Roles](/images/5-Workshop/5.5-Backend-Serverless/iam_roles_setup.png)

---

### Bước 3: Tạo và Triển khai các Lambda Functions

1. Mở [AWS Lambda Console](https://console.aws.amazon.com/lambda/).
2. Chọn **Create function**.
3. Trong biểu mẫu tạo hàm:
   * Chọn **Author from scratch**.
   * **Runtime:** Chọn `Node.js 20.x` (hoặc phiên bản Node khác tương thích với dự án).
   * Cuộn xuống mục **Custom settings** -> nhấp chuột để mở rộng phần **Additional settings**.
   * Bên dưới mục **General** bên trong Additional settings:
     * Gạt nút **ARM64 architecture** sang **On** (để sử dụng chip ARM64/Graviton2 giúp tối ưu chi phí và tăng tốc độ xử lý).
     * Gạt nút **Custom execution role** sang **On** (để cấu hình sử dụng Role đã tạo trước đó).

#### A. Tạo hàm `SmartImage-ApiHandler`
* **Function name:** Điền `SmartImage-ApiHandler`.
* **Custom execution role:** Tìm chọn `SmartImage-ApiHandler-Role` ở ô lựa chọn dropdown vừa hiển thị.
* Chọn **Create function**.
* **Code & Deployment:** Zip mã nguồn từ thư mục `backend/api-handler/` và upload lên thông qua tab **Code**.
* **Environment variables:** Tại tab *Configuration -> Environment variables*, thêm các biến:
   * `IMAGE_TABLE_NAME` = `SmartImage-Images`
   * `USER_QUOTA_TABLE_NAME` = `SmartImage-UserQuotas`
   * `USER_PROFILE_TABLE_NAME` = `SmartImage-UserProfiles`
   * `RAW_BUCKET_NAME` = `smartimage-raw-bucket-<ten-cua-ban>`
   * `PROCESSED_BUCKET_NAME` = `smartimage-processed-bucket-<ten-cua-ban>`
   * `USER_POOL_ID` = `<user-pool-id-cognito-cua-ban>`

#### B. Tạo hàm `SmartImage-ImageProcessor`
* **Function name:** Điền `SmartImage-ImageProcessor`.
* **Custom execution role:** Tìm chọn `SmartImage-ImageProcessor-Role`.
* Chọn **Create function**.
* **Code & Deployment:** Zip mã nguồn từ thư mục `backend/image-processor/` (đã cài đặt thư viện `Sharp` biên dịch sẵn tương thích với môi trường Linux/Lambda) và upload lên.
* **Environment variables:**
   * `IMAGE_TABLE_NAME` = `SmartImage-Images`
   * `PROCESSED_BUCKET_NAME` = `smartimage-processed-bucket-<ten-cua-ban>`

#### C. Tạo hàm `SmartImage-AIAnalyzer`
* **Function name:** Điền `SmartImage-AIAnalyzer`.
* **Custom execution role:** Tìm chọn `SmartImage-AIAnalyzer-Role`.
* Chọn **Create function**.
* **Code & Deployment:** Zip mã nguồn từ thư mục `backend/ai-analyzer/` và upload lên.
* **Environment variables:**
   * `IMAGE_TABLE_NAME` = `SmartImage-Images`

![Danh sách Lambda Functions](/images/5-Workshop/5.5-Backend-Serverless/lambda_list.png)

---

### Bước 4: Cấu hình các bộ kích hoạt (Triggers)

#### A. Cấu hình S3 Event Notification (Kích hoạt ImageProcessor)
1. Quay lại S3 Console, bấm chọn bucket `smartimage-raw-bucket-<ten-cua-ban>`.
2. Chuyển sang tab **Properties**.
3. Cuộn xuống mục **Event notifications** và chọn **Create event notification**.
4. **Event name:** Điền `TriggerImageProcessor`.
5. **Prefix:** Điền **`users/`** (Bắt buộc, để Lambda chỉ kích hoạt khi người dùng tải ảnh lên trong thư mục được phân quyền cụ thể của họ).
6. **Event types:** Tích chọn **All object create events** (`s3:ObjectCreated:*`).
7. **Destination:** Chọn **Lambda function** và chỉ định hàm `SmartImage-ImageProcessor`.
8. Chọn **Save changes**.

![Cấu hình S3 Event Trigger](/images/5-Workshop/5.5-Backend-Serverless/s3_trigger_setup.png)

#### B. Cấu hình DynamoDB Stream Trigger (Kích hoạt AIAnalyzer)
1. Truy cập DynamoDB Console, chọn mục **Tables**.
2. Bấm vào bảng `SmartImage-Images` và chọn tab **Exports and streams**.
3. Tại mục **DynamoDB stream details**, chọn **Create trigger**.
4. **Lambda function:** Chọn hàm `SmartImage-AIAnalyzer`.
5. **Batch size:** Điền `1` (để xử lý ảnh ngay tức thì).
6. Giữ các thông số khác mặc định và chọn **Create trigger**.

![Cấu hình DynamoDB Stream Trigger](/images/5-Workshop/5.5-Backend-Serverless/dynamodb_trigger_setup.png)

---

### Bước 5: Cấu hình Amazon API Gateway

1. Mở [API Gateway Console](https://console.aws.gateway.aws.amazon.com/apigateway/ or https://console.aws.amazon.com/apigateway/).
2. Chọn **Create API** -> Chọn **REST API** -> Chọn **Build**.
3. **API name:** Điền `SmartImage-API`.
4. Chọn **Create API**.
5. **Cấu hình Cognito Authorizer:**
   * **Lưu ý:** Để nhìn thấy menu điều hướng riêng của API (trong đó có mục "Authorizers"), trước tiên bạn phải **bấm chọn tên API của bạn** (ví dụ `SmartImage-API-production` hoặc `SmartImage-API-staging`) từ danh sách API hiển thị ở trang chủ.
   * Tại thanh menu bên trái vừa xuất hiện riêng cho API đã chọn, chọn **Authorizers** -> **Create authorizer**.
   * **Name:** Điền `CognitoAuth`.
   * **Type:** Chọn **Cognito**.
   * **Cognito User Pool:** Chọn `SmartImage-UserPool`.
   * **Token source:** Điền `Authorization` (đọc từ header của HTTP request).
   * Bấm **Create authorizer**.
 6. **Cấu hình Resource & Method Proxy:**
   * Chọn mục **Resources** ở thanh menu trái riêng của API.
   * Bấm chọn thư mục gốc `/` (hoặc `/v1` nếu bạn muốn triển khai dưới đường dẫn `/v1`) trong cây thư mục Resources.
   * Chọn nút **Create resource** (nút màu trắng nằm ngay phía trên danh sách cây thư mục Resources).
   * **Proxy resource:** Gạt nút này sang **On** (hệ thống sẽ tự động cấu hình nó thành tài nguyên proxy `{proxy+}` và tự động thêm method `ANY`).
   * **CORS (Cross-Origin Resource Sharing):** Gạt nút này sang **On**.
   * Chọn **Create resource**.
   * Sau khi tài nguyên được tạo, bấm chọn method **ANY** nằm dưới thư mục `{proxy+}` vừa tạo trong cây thư mục (nếu hệ thống không tự động mở bảng cấu hình method, hãy bấm **Create method** dưới thư mục đó).
   * Tại màn hình cấu hình chi tiết Method / Integration:
     * **Integration type:** Chọn **Lambda function** (Đây chính là vị trí hiển thị ô chọn Integration type!).
     * **Lambda proxy integration:** Gạt nút này sang **On** (Rất quan trọng).
     * **Lambda function:** Chọn vùng Singapore và tìm chọn hàm `SmartImage-ApiHandler`.
     * Chọn **Create method** (hoặc **Save**).
 7. **Bảo mật API Methods:**
   * Bấm chọn method `ANY` dưới resource `/v1/{proxy+}` (hoặc `{proxy+}`) trong cây thư mục.
   * Chuyển sang tab **Method request**.
   * Nhấp chọn **Edit**.
   * **Authorization:** Chọn `CognitoAuth` từ ô lựa chọn dropdown.
   * Chọn **Save**.
 8. **Deploy API:**
   * Chọn nút **Deploy API** ở góc trên cùng bên phải giao diện quản lý Resources.
   * **Stage:** Chọn **[New Stage]**, điền tên stage là `dev`.
   * Chọn **Deploy**.

![Cấu hình API Gateway Authorizer](/images/5-Workshop/5.5-Backend-Serverless/api_gateway_authorizer.png)

   * *Hãy ghi lại **Invoke URL** của API Gateway (ví dụ: `https://xxxx.execute-api.ap-southeast-1.amazonaws.com/dev`). Bạn sẽ dùng nó để cấu hình React frontend.*
