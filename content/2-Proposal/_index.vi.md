---
title: "Bản đề xuất"
date: 2026-07-01
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Smart Image Platform
## Nền tảng lưu trữ và xử lý ảnh theo kiến trúc AWS Serverless và Event-Driven

### 1. Tóm tắt

**Smart Image Platform** hỗ trợ tải lên, lưu trữ, xử lý và phân tích nội dung hình ảnh trên AWS. Hệ thống sử dụng kiến trúc serverless và hướng sự kiện để tách các yêu cầu tương tác với người dùng khỏi những tác vụ xử lý ảnh mất nhiều thời gian.

Ứng dụng React được triển khai bằng AWS Amplify Hosting. Amazon Cognito đảm nhiệm đăng ký, xác minh email, đăng nhập và phân quyền người dùng. API Gateway, Lambda, Amazon S3, DynamoDB Streams và Amazon Rekognition tạo thành luồng xử lý chính. AWS WAF bảo vệ API REST, trong khi CloudWatch, X-Ray và Amazon SNS hỗ trợ theo dõi và cảnh báo vận hành.

Toàn bộ hạ tầng backend được định nghĩa bằng AWS CDK, giúp cấu hình giữa các môi trường có thể được quản lý và triển khai lặp lại. Phiên bản hiện tại sử dụng S3 presigned URL để tải ảnh lên và đọc ảnh riêng tư; CloudFront dành riêng cho processed bucket chưa được bật.

### 2. Vấn đề và giải pháp

* **Vấn đề:** Khi resize, trích xuất metadata và phân tích AI được thực hiện đồng bộ trên cùng một máy chủ, thời gian phản hồi có thể tăng theo kích thước ảnh và số lượng yêu cầu. Việc tự quản lý máy chủ cũng làm phát sinh công việc cấp phát tài nguyên, mở rộng hệ thống, vá lỗi và giám sát.
* **Giải pháp:** Kết hợp xử lý đồng bộ và bất đồng bộ. Các thao tác xác thực, gọi API và yêu cầu presigned URL được xử lý đồng bộ. Sau khi ảnh được tải trực tiếp lên raw S3 bucket, các tác vụ resize, tạo thumbnail, trích xuất metadata và phân tích Rekognition được thực hiện bất đồng bộ qua S3 Event Notification, Lambda và DynamoDB Streams.
* **Lợi ích:** Giảm lượng dữ liệu ảnh đi qua API backend, tách tác vụ xử lý nặng khỏi request của người dùng và chỉ sử dụng tài nguyên tính toán khi có sự kiện. Việc phân loại và kiểm duyệt ảnh được tự động hóa, nhưng kết quả Rekognition vẫn cần được xem là dữ liệu hỗ trợ thay vì thay thế hoàn toàn việc kiểm tra của con người.

### 3. Kiến trúc giải pháp

Sơ đồ dưới đây mô tả luồng xử lý cốt lõi. Để giữ sơ đồ dễ đọc, các thành phần hỗ trợ như SQS dead-letter queue, CloudWatch, X-Ray và SNS không được thể hiện đầy đủ.

![Smart Image Platform Architecture](/images/2-Proposal/image.png)

* **Luồng xử lý:**

  1. Người dùng truy cập ứng dụng React được triển khai bằng **AWS Amplify Hosting**.
  2. Ứng dụng sử dụng **Amazon Cognito User Pool** để đăng ký, xác minh email và đăng nhập. Sau khi xác thực thành công, client nhận JSON Web Token để gọi API.
  3. Client gửi request kèm token đến **Amazon API Gateway**. **AWS WAF** kiểm tra request trước API; Cognito Authorizer của API Gateway xác minh token trước khi cho phép gọi Lambda.<br> 3.1. **Amazon API Gateway** chuyển Token sang **Amazon Cognito Authorizer** để xác thực tính hợp lệ và quyền hạn của người dùng trước khi xử lý request.
  4. 4.a. Sau khi xác thực thành công, **Amazon API Gateway** gọi hàm **AWS Lambda (Presigned URL Generator)**. <br> 4.b. **AWS Lambda (Presigned URL Generator)** khởi tạo bản ghi dữ liệu ban đầu vào **DynamoDB**, đồng thời tạo ra một đường dẫn S3 Presigned URL có thời hạn an toàn và trả về cho Client qua **API Gateway**.
  5. **Amazon Amplify** dùng đường dẫn Presigned URL vừa nhận được để tải trực tiếp tệp ảnh gốc từ thiết bị lên kho lưu trữ **S3 Bucket**.
  6. Sự kiện ảnh tải thành công lên **S3 Bucket** tự động kích hoạt (trigger) hàm **AWS Lambda (Image processor Lambda)**.
  7. 7.a. **AWS Lambda (Image processor Lambda)** thực hiện các tác vụ xử lý ảnh cơ bản (như nén, resize) và lưu tệp ảnh hoàn thiện vào **processed S3 Bucket**. <br> 7.b. Đồng thời, hàm **AWS Lambda (Image processor Lambda)** ghi cập nhật trạng thái xử lý ảnh vào cơ sở dữ liệu **DynamoDB**.
  8. Sự kiện thay đổi dữ liệu tại **DynamoDB** kích hoạt hàm **AWS Lambda (AI Analyzer Lambda)** để bắt đầu quy trình phân tích hình ảnh nâng cao.
  9. Hàm **AWS Lambda (AI Analyzer Lambda)** gọi API dịch vụ **Amazon Rekognition** để trích xuất các nhãn (labels), vật thể, nhận diện khuôn mặt, sau đó lưu toàn bộ dữ liệu phân tích (Metadata) ngược lại vào **DynamoDB**.
  

### 4. Triển khai kỹ thuật

* **Các giai đoạn thực hiện:**

  1. *Nghiên cứu và thiết kế:* Phân tích luồng tải ảnh, S3 presigned URL, xử lý hướng sự kiện, xác thực và mô hình dữ liệu.
  2. *Xây dựng hạ tầng:* Định nghĩa các stack CDK cho Storage, Database, Authentication, API, Monitoring và Amplify Hosting; cấu hình AWS Budgets để theo dõi chi phí.
  3. *Phát triển ứng dụng:* Xây dựng frontend, API Handler, Image Processor và AI Analyzer; tích hợp Rekognition, Cognito và các dịch vụ lưu trữ.
  4. *Kiểm thử và triển khai:* Chạy unit test, kiểm thử end-to-end cho đăng ký, tải ảnh, xử lý ảnh và phân tích AI; triển khai môi trường staging và production. Việc đánh giá tải lớn cần được thực hiện riêng trước khi công bố năng lực người dùng đồng thời.

* **Công nghệ đang sử dụng:**

  * *Frontend:* React 19 và Vite; `amazon-cognito-identity-js` quản lý phiên Cognito; Zustand quản lý trạng thái; native `fetch` gọi REST API và truyền dữ liệu ảnh qua presigned URL.
  * *Backend:* TypeScript chạy trên Node.js Lambda; AWS SDK for JavaScript v3; Sharp và `exif-reader` xử lý ảnh và metadata.
  * *Hạ tầng:* AWS CDK bằng TypeScript; DynamoDB On-Demand với Point-in-Time Recovery; hai S3 bucket private bật Block Public Access; API Gateway REST API; Cognito User Pool; SQS dead-letter queue; WAF; CloudWatch; X-Ray và SNS.
  * *Bảo vệ dữ liệu:* Client chỉ được cấp presigned URL có thời hạn cho thao tác cụ thể. Lambda sử dụng IAM role theo quyền cần thiết; S3 không cho phép truy cập công khai.

### 5. Lộ trình và mốc triển khai

* **Giai đoạn 1:** Hoàn thành nghiên cứu nền tảng AWS, IAM, Budgets, S3, Lambda, DynamoDB, API Gateway, Cognito và các mô hình serverless liên quan.
* **Giai đoạn 2:** Hoàn thiện kiến trúc, mã nguồn CDK và chuỗi backend xử lý ảnh từ S3 đến Rekognition.
* **Giai đoạn 3:** Tích hợp frontend, xác thực, phân quyền, thư viện ảnh và trang quản trị; bổ sung WAF, logging, alarms và thông báo SNS.
* **Giai đoạn 4:** Kiểm thử end-to-end, triển khai staging/production, kết nối tên miền và hoàn thiện tài liệu workshop cùng video demo.

### 6. Ước tính ngân sách

Tài khoản triển khai được tạo sau ngày 15/07/2025 và thuộc chương trình AWS Free Tier mới. Tài khoản nhận 100 USD credits khi đăng ký và có thể nhận thêm tối đa 100 USD khi hoàn thành các hoạt động do AWS quy định. Free Plan kết thúc sau tối đa sáu tháng hoặc khi credits hết, tùy điều kiện nào đến trước. Credits chưa sử dụng hết có thể tiếp tục được áp dụng sau khi nâng cấp lên Paid Plan nhưng hết hạn sau 12 tháng kể từ ngày tạo tài khoản.

Ước tính dưới đây sử dụng môi trường `ap-southeast-1`, tối đa 5.000 ảnh mới mỗi tháng, kích thước trung bình 3 MB cho ảnh gốc và tổng 1 MB cho thumbnail cùng ảnh resized. Mỗi ảnh được gọi một lần với DetectLabels và một lần với DetectModerationLabels. Lưu lượng frontend và API được giả định ở quy mô MVP, không có CloudFront riêng cho processed bucket.

| Thành phần | Ước tính sau khi hết credits | Cơ sở ước tính |
|---|---:|---|
| AWS WAF | 7–7,10 USD/tháng | Một Web ACL, một AWS Managed Rules group, một rate-based rule và lưu lượng dưới một triệu request |
| Amazon Rekognition | 1–2 USD ở 1.000 ảnh; 9–10 USD ở 5.000 ảnh | Hai API thuộc Group 2 được gọi cho mỗi ảnh; free usage hiện hành được tính theo điều kiện của tài khoản |
| Amazon S3 | 0,50–1,50 USD/tháng đầu | Khoảng 20 GB dữ liệu mới ở mức 5.000 ảnh, cộng request; chi phí lưu trữ tăng theo dữ liệu tích lũy và giảm dần khi raw object chuyển sang IA/Glacier |
| AWS Lambda | 0–1 USD/tháng | Bốn Lambda; Image Processor dùng 1.536 MB và có thời gian chạy phụ thuộc kích thước ảnh |
| API Gateway và DynamoDB | 0–1 USD/tháng | Lưu lượng MVP, ba bảng On-Demand, DynamoDB Streams và PITR |
| CloudWatch, X-Ray, SNS và SQS | 0,20–4 USD/tháng | Log retention 14–30 ngày, 12 standard alarms, một dashboard, trace, hai DLQ và email notification |
| Amplify Hosting | 0–1 USD/tháng | SPA nhỏ, số lần build và data transfer thấp |
| Amazon Cognito | 0 USD/tháng | Số người dùng hoạt động hàng tháng ở quy mô MVP nằm dưới hạn mức miễn phí áp dụng |

* **Trong thời gian Free Plan/credits còn hiệu lực:** chi phí dịch vụ vẫn được ghi nhận nhưng được credits bù trừ. Số tiền phải thanh toán dự kiến khoảng **0 USD/tháng**, với điều kiện credits chưa hết và không phát sinh lưu lượng bất thường.
* **Sau khi hết credits:** khoảng **10–14 USD/tháng** ở mức tối đa 1.000 ảnh mới, hoặc khoảng **18–25 USD/tháng** khi gần 5.000 ảnh mới. Đây là khoảng dự toán, không phải mức giá cố định.
* **Chi phí tăng theo thời gian:** raw bucket chuyển sang Standard-IA sau 90 ngày và Glacier sau 365 ngày, nhưng processed bucket không có quy tắc hết hạn. Dung lượng tích lũy cần được theo dõi riêng.
* **Custom domain:** tên miền và DNS được quản lý bởi nhà cung cấp bên ngoài AWS. Dự án không tạo Route 53 hosted zone, vì vậy không tính Route 53 hay phí đăng ký tên miền vào ngân sách AWS này.

Số tiền thực tế cần được xác nhận bằng AWS Pricing Calculator, Billing và Cost Explorer của tài khoản triển khai. Ước tính chưa bao gồm thuế, phí của nhà cung cấp tên miền và trường hợp vượt service quota hoặc phát sinh lưu lượng bất thường.

Tham khảo: [AWS Free Tier](https://aws.amazon.com/free/), [AWS WAF Pricing](https://aws.amazon.com/waf/pricing/), [Amazon Rekognition Pricing](https://aws.amazon.com/rekognition/pricing/) và [Amazon API Gateway Pricing](https://aws.amazon.com/api-gateway/pricing/).

### 7. Đánh giá rủi ro

* **Vòng lặp xử lý S3:** Nếu Lambda ghi kết quả trở lại cùng vị trí đang phát sinh trigger, hàm có thể tự kích hoạt liên tục.
  * *Biện pháp:* Tách raw bucket và processed bucket. Chỉ raw bucket có S3 notification đến Image Processor và trigger được giới hạn ở prefix `users/`.
* **Lỗi xử lý bất đồng bộ:** Sự kiện Lambda có thể thất bại do file không hợp lệ, timeout hoặc lỗi dịch vụ phụ thuộc.
  * *Biện pháp:* Cấu hình retry và SQS dead-letter queue; sử dụng CloudWatch alarm và SNS để thông báo lỗi.
* **API spam hoặc lưu lượng bất thường:** Request lặp lại có thể làm tăng số lần gọi API Gateway và Lambda.
  * *Biện pháp:* WAF áp dụng AWS Managed Rules Common Rule Set và rate-based rule giới hạn 2.000 request cho mỗi địa chỉ IP trong cửa sổ đánh giá của WAF. API Gateway stage được cấu hình rate limit 1.000 request/giây và burst 500. Đây là giới hạn cấp stage, không phải hạn mức theo API key.
* **Rò rỉ dữ liệu S3:** Object có thể bị truy cập ngoài phạm vi người dùng được phép.
  * *Biện pháp:* Bật Block Public Access, dùng IAM least privilege, kiểm tra quyền sở hữu ở API Handler và chỉ cấp presigned GET/PUT URL có thời hạn.
* **Kết quả AI không chính xác:** Nhãn hoặc kết quả kiểm duyệt có thể có false positive hoặc false negative.
  * *Biện pháp:* Lưu confidence score, sử dụng ngưỡng cấu hình và cho phép quản trị viên xem xét nội dung khi cần.
* **Chi phí ngoài dự kiến:** Log retention, PITR, WAF, dữ liệu S3 tích lũy hoặc chuỗi xử lý lặp lại có thể làm tăng hóa đơn và tiêu hao credits.
  * *Biện pháp:* Cấu hình AWS Budgets, retention phù hợp, CloudWatch alarms và kiểm tra Cost Explorer định kỳ.

### 8. Kết quả kỳ vọng

* Người dùng có thể đăng ký, xác minh email, đăng nhập, quản lý hồ sơ và tải ảnh lên qua giao diện web.
* Ảnh được lưu trong bucket private, tạo thumbnail và phiên bản resized, trích xuất metadata, nhận diện nhãn và kiểm duyệt nội dung theo luồng bất đồng bộ.
* API cung cấp tìm kiếm, lọc, phân trang, thư viện cá nhân, thư viện cộng đồng và các chức năng quản trị theo quyền Cognito.
* Hạ tầng có thể được triển khai lặp lại bằng CDK cho staging và production, đồng thời cung cấp log, trace, metrics và cảnh báo vận hành.
* Các dịch vụ managed có khả năng mở rộng trong giới hạn service quota. Năng lực tải cụ thể chỉ được công bố sau khi có kết quả load test và đánh giá quota tương ứng.
