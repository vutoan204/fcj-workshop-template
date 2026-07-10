---
title: "Kiểm thử & Xác minh luồng hoạt động"
date: 2024-01-01
weight: 7
chapter: false
pre: " <b> 5.7. </b> "
---


# Kiểm thử & Xác minh hoạt động Smart Image Platform

Sau khi cấu hình xong toàn bộ kiến trúc serverless, hãy thực hiện các bước sau để kiểm thử luồng hoạt động bất đồng bộ từ client lên AWS cloud, kiểm tra log thực thi, đo lường các chỉ số hệ thống và thử nghiệm kịch bản lỗi.

---

### Bước 1: Đăng ký tài khoản người dùng & Đăng nhập

1. Mở trình duyệt web, truy cập đường link **Domain URL** của AWS Amplify đã tạo ở bước trước.
2. Bấm chọn **Sign Up** để tạo tài khoản mới.
3. Điền các thông tin: **Họ tên (Full Name)**, **Địa chỉ Email**, và **Mật khẩu**. Chọn **Sign Up**.
4. Kiểm tra hộp thư email của bạn. Bạn sẽ nhận được một email tự động từ Cognito chứa mã xác nhận kích hoạt tài khoản.
5. Nhập mã code này vào trang xác thực trên web để kích hoạt tài khoản thành công.
6. Quay lại màn hình đăng nhập, thực hiện nhập email và mật khẩu vừa tạo để vào hệ thống.

![Giao diện đăng nhập React App](/images/5-Workshop/5.7-Testing-Validation/react_app_login.png)

---

### Bước 2: Tải lên một hình ảnh

1. Tại giao diện dashboard của người dùng, chọn nút **Upload Image**.
2. Chọn một tệp ảnh JPEG/PNG bất kỳ trên máy tính cá nhân (ví dụ chọn ảnh chụp phong cảnh có núi, hồ nước, xe cộ, v.v.).
3. Nhấp **Upload**.
4. Ứng dụng client sẽ gọi API Gateway lấy S3 Presigned PUT URL và tải ảnh trực tiếp lên Raw S3 bucket dưới tiền tố thư mục `users/<userId>/`, sau đó chuyển giao diện sang trạng thái tải thành công.

---

### Bước 3: Xác minh bộ kích hoạt xử lý ảnh trên S3 (S3 Trigger)

1. Mở [Amazon S3 Console](https://s3.console.aws.amazon.com/).
2. Bấm chọn bucket `smartimage-processed-bucket-<ten-cua-ban>`.
3. Kiểm tra danh sách tệp. Bạn sẽ thấy một folder mới được tạo chứa ảnh thumbnail của tệp ảnh bạn vừa upload (ảnh đã được thu nhỏ kích thước và nén dung lượng, thường có hậu tố `-thumbnail`).
4. Điều này chứng minh rằng **S3 Trigger** đã hoạt động chính xác, tự động kích hoạt hàm Lambda `SmartImage-ImageProcessor` để xử lý ảnh ngay khi ảnh gốc được tải lên.

![Tệp ảnh được tạo trong S3 Processed Bucket](/images/5-Workshop/5.7-Testing-Validation/s3_processed_objects.png)

---

### Bước 4: Xác minh luồng gắn nhãn AI trên DynamoDB

1. Mở [Amazon DynamoDB Console](https://console.aws.amazon.com/dynamodb/).
2. Chọn mục **Tables** -> Chọn bảng `SmartImage-Images` và chọn **Explore table items**.
3. Tìm bản ghi tương ứng với ID tài khoản của bạn và tệp ảnh vừa upload.
4. Kiểm tra các thuộc tính (attributes) bên trong bản ghi này:
   * `status`: Trạng thái cần chuyển thành `COMPLETED` hoặc `PROCESSED`.
   * `tags` (hoặc nhãn): Chứa danh sách các thẻ nhãn do dịch vụ Amazon Rekognition tự động nhận diện (ví dụ: `["Mountain", "Sky", "Nature", "Outdoor"]`).
   * `moderationStatus`: Trạng thái cần hiển thị `APPROVED` hoặc `SAFE`.
5. Điều này xác minh rằng:
   * Bản ghi DynamoDB đã được hàm Image Processor cập nhật thành công.
   * **DynamoDB Stream Trigger** đã kích hoạt thành công hàm Lambda `SmartImage-AIAnalyzer`.
   * Hàm AI Analyzer đã gọi **Amazon Rekognition** thành công để bóc nhãn và lưu ngược kết quả lại DynamoDB.

![Siêu dữ liệu DynamoDB và nhãn AI](/images/5-Workshop/5.7-Testing-Validation/dynamodb_item_tags.png)

---

### Bước 5: Kiểm tra tính năng tìm kiếm & Gallery trên Frontend

1. Quay lại ứng dụng React Web.
2. Tải lại trang (F5) hoặc chuyển sang tab **Gallery**. Frontend sẽ gửi yêu cầu lấy danh sách ảnh từ endpoint `/v1/images` kích hoạt hàm `api-handler` Lambda. Lambda thực hiện truy vấn DynamoDB và tạo ra các **S3 Presigned GET URL** ngắn hạn bảo mật cho tất cả các ảnh thumbnail.
3. Bạn sẽ thấy hình ảnh vừa upload hiển thị trên giao diện gallery.
4. Nhấp vào ảnh để xem chi tiết, danh sách các nhãn AI tương ứng sẽ hiển thị ngay bên dưới ảnh.
5. Nhấp nút tải/xem ảnh gốc, frontend gửi request và backend sẽ tạo một S3 Presigned GET URL ngắn hạn trỏ thẳng tới file ảnh thô trong Raw S3 bucket để tải xuống an toàn.
6. Nhập tên nhãn (ví dụ: `Mountain`) vào thanh tìm kiếm và chọn **Search**. Đảm bảo danh sách gallery tự động lọc hiển thị đúng các ảnh có chứa tag đó.

![Giao diện Dashboard hiển thị ảnh và nhãn AI](/images/5-Workshop/5.7-Testing-Validation/react_app_dashboard.png)

---

### Bước 6: Kiểm tra Log thực thi hệ thống (CloudWatch Logs)

Để xác thực luồng chạy chi tiết của các hàm Lambda ngầm:
1. Mở [Amazon CloudWatch Console](https://console.aws.amazon.com/cloudwatch/).
2. Ở menu bên trái, chọn **Logs** -> **Log groups**.
3. Tìm kiếm nhóm log `/aws/lambda/SmartImage-ImageProcessor`. Bấm chọn và mở luồng log mới nhất.
4. Xác minh log hiển thị thông tin xử lý thành công: e.g. Khởi chạy `sharp`, tải file thô từ S3, nén ảnh, đẩy ảnh processed lên S3 và cập nhật trạng thái DynamoDB.
5. Thực hiện tương tự đối với nhóm log `/aws/lambda/SmartImage-AIAnalyzer` để kiểm tra kết quả trả về từ **Amazon Rekognition API**.

---

### Bước 7: Kiểm tra Metrics trên Operational Dashboard

Nếu bạn đã triển khai monitoring stack:
1. Tại CloudWatch Console, bấm chọn mục **Dashboards** ở menu bên trái.
2. Mở dashboard tên là `SmartImage-dev-Operations`.
3. Kiểm tra các biểu đồ đồ thị (**Graph Widgets**) để đo lường các thông số:
   * **Invocations:** Lượng request gọi Lambda tương ứng với số ảnh bạn đã upload.
   * **Errors:** Mặc định bằng `0` (trạng thái hệ thống khỏe mạnh).
   * **Duration:** Thời gian phản hồi trung bình và P95 của các hàm Lambda.
   * **API Gateway Latency:** Độ trễ kết nối API nằm trong ngưỡng an toàn dưới 1 giây.

---

### Bước 8: Kiểm thử kịch bản Lỗi (Error & Failure Testing)

Chúng ta giả lập tình huống lỗi để kiểm tra khả năng bắt lỗi và đưa thông tin vào hàng đợi Dead Letter Queue (DLQ):
1. **Tải lên một tệp không được hỗ trợ:**
   * Truy cập giao diện React dashboard, chọn **Upload Image**.
   * Chọn một tệp tin văn bản (ví dụ `test.txt`) hoặc một tệp nén `.zip` (thay vì ảnh JPEG/PNG) để upload.
   * Client sẽ cố gắng gửi tệp lên hệ thống thông qua Presigned URL.
2. **Kiểm tra Logs lỗi của Image Processor:**
   * Mở CloudWatch Log Group `/aws/lambda/SmartImage-ImageProcessor`.
   * Mở stream log mới nhất vừa sinh ra. Bạn sẽ thấy dòng log ghi nhận lỗi xử lý định dạng tệp: e.g. `Error: Invalid file format` hoặc `Sharp: Input buffer has an unsupported image format`.
3. **Đo lường sai số trên Dashboard & Alarms:**
   * Vào xem Dashboard `SmartImage-dev-Operations`. Cột biểu đồ **Errors** của Image Processor sẽ tăng lên `1`.
   * Nếu kích hoạt lỗi liên tục vượt quá ngưỡng cho phép của CloudWatch Alarm (ví dụ: >5 lỗi/5 phút), Alarm tương ứng `SmartImage-dev-ImageProcessor-Errors` sẽ chuyển sang trạng thái **In Alarm** (Cảnh báo).
   * Hệ thống sẽ tự động gửi một email cảnh báo đến địa chỉ email quản trị viên của bạn thông qua SNS.
4. **Kiểm tra hàng đợi Dead Letter Queue (DLQ):**
   * Mở [Amazon SQS Console](https://console.aws.amazon.com/sqs/).
   * Chọn hàng đợi tên là `SmartImage-ImageProcessorDlq-dev`.
   * Bạn sẽ thấy số lượng tin nhắn chờ duyệt (`Messages Available`) tăng lên đại diện cho request lỗi vừa bị đẩy từ Lambda sang để quản trị viên kiểm tra.
