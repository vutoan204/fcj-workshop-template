---
title: "Bản đề xuất"
date: 2026-07-01
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Smart Image Platform  
## Giải pháp AWS Serverless & Event-Driven toàn diện cho hệ thống lưu trữ và xử lý ảnh thông minh  

### 1. Tóm tắt điều hành  
**Smart Image Platform** là giải pháp điện toán đám mây cao cấp được thiết kế nhằm mục đích tự động hóa quy trình tải lên, lưu trữ an toàn, nén và phân tích nội dung hình ảnh thông qua Trí tuệ nhân tạo (AI). Nền tảng tận dụng tối đa sức mạnh của kiến trúc không máy chủ (**AWS Serverless**) và hướng sự kiện (**Event-Driven**) nhằm đảm bảo tính sẵn sàng cao, khả năng mở rộng quy mô linh hoạt và tối ưu hóa chi phí vận hành. Toàn bộ hệ thống giao diện và phân hệ API được bảo mật nghiêm ngặt từ tầng biên, giới hạn quyền truy cập thông qua Amazon Cognito dành cho các tài khoản nội bộ tổ chức.

### 2. Tuyên bố vấn đề  
* **Vấn đề hiện tại:** Các hệ thống quản lý và xử lý tệp tin truyền thống thường gặp khó khăn lớn trong việc co dãn tài nguyên khi lượng tệp tin tải lên tăng đột biến. Quy trình resize ảnh, trích xuất metadata và gắn thẻ (tagging) phân loại ảnh nếu xử lý đồng bộ trên server truyền thống sẽ gây nghẽn băng thông, tiêu tốn tài nguyên phần cứng lớn và tăng độ trễ (latency) cho người dùng cuối. 
* **Giải pháp:** Xây dựng một kiến trúc bất đồng bộ hoàn toàn dựa trên sự kiện kích hoạt của AWS. Người dùng thực hiện tương tác qua giao diện Web được host trên **Amazon Amplify**, xác thực qua Amazon Cognito. Mọi yêu cầu được bảo vệ bởi tường lửa AWS WAF đứng trước Amazon API Gateway. Logic lưu trữ thô sử dụng Amazon S3, kích hoạt tự động các chuỗi Lambda function ngầm: một nhánh xử lý nén/resize ảnh tối ưu dung lượng đẩy sang bucket lưu trữ kết quả, một nhánh kích hoạt Amazon Rekognition AI tự động bóc tách nhãn vật thể và ghi nhận kết quả bất đồng bộ vào cơ sở dữ liệu Amazon DynamoDB. Cuối cùng, **Amazon CloudFront** đứng trước lớp lưu trữ để phân phối nội dung ảnh đã xử lý về lại người dùng với độ trễ thấp và bảo mật cao.
* **Lợi ích và hoàn vốn đầu tư (ROI):** Loại bỏ hoàn toàn chi phí duy trì phần cứng server cố định 24/7 nhờ cơ chế Pay-as-you-go (chỉ trả tiền trên từng mili-giây chạy code và dung lượng lưu trữ thực tế). Tiết kiệm đến 80% chi phí hạ tầng trong giai đoạn đầu nhờ nằm trọn trong gói AWS Free Tier. Hệ thống tự động hóa hoàn toàn quy trình phân loại ảnh bằng AI, giúp giảm tải 100% thao tác thủ công và nâng cao độ chính xác của metadata lưu trữ.

### 3. Kiến trúc giải pháp  
Nền tảng sử dụng các dịch vụ Serverless phối hợp chặt chẽ theo luồng dữ liệu số hóa tuần tự, được biểu diễn qua sơ đồ luồng hoạt động dưới đây:

![Smart Image Platform Architecture](/images/2-Proposal/architecture.jpeg)

* **Chi tiết luồng vận hành (Execution Flow):**
  1. Người dùng **(Users)** truy cập giao diện ứng dụng frontend được host trên **Amazon Amplify**.
  2. Amazon Amplify gửi yêu cầu xác thực và đăng nhập tới **Amazon Cognito**. Sau khi đăng nhập thành công, người dùng nhận về ID Token hợp lệ.
  3. Amazon Amplify gửi request kèm Token thông qua tường lửa **AWS WAF** đứng bảo vệ tầng biên, đồng thời **Amazon Cognito** thực hiện xác thực song song với **Amazon API Gateway** (bước **3.1**) thông qua Cognito Authorizer để kiểm tra quyền truy cập trước khi request được chuyển tiếp.
  4.Lớp thực thi API:
     * **4.a:** API Gateway kích hoạt **AWS Lambda (API Handler Lambda)** để xử lý logic và khởi tạo S3 Presigned URL an toàn cấp quyền cho Client.
     * **4.b:** Song song đó, API Handler Lambda ghi nhận một bản ghi khởi tạo (init record) của yêu cầu vào **Amazon DynamoDB** để theo dõi trạng thái request.
  5. Client (Amazon Amplify) sử dụng Presigned URL để trực tiếp tải dữ liệu nhị phân của hình ảnh lên **S3 Bucket (Raw Store)**, giảm tải tối đa cho server.
  6. Sự kiện file ảnh được ghi nhận thành công vào **S3 Bucket** ngay lập tức kích hoạt hàm **AWS Lambda (Image processor Lambda)** chạy ngầm bất đồng bộ.
  7. Hàm Lambda tiến hành hai tác vụ song song:
     * **7.a:** Thực hiện tối ưu hóa dung lượng, nén, resize ảnh và đẩy tệp ảnh kết quả sang lưu trữ an toàn tại **processed S3 Bucket**.
     * **7.b:** Thực hiện trích xuất dữ liệu metadata cơ bản của tệp ảnh (kích thước, định dạng, ngày tạo) và ghi bản ghi vào **Amazon DynamoDB** (bảng dữ liệu tại phân hệ AI Analysis).
  8. Sự kiện tạo mới/cập nhật bản ghi metadata trong DynamoDB (thông qua DynamoDB Streams) kích hoạt **AWS Lambda (AI Analyzer Lambda)** xử lý phân tích nâng cao.
  9. Hàm Lambda gọi trực tiếp đến dịch vụ trí tuệ nhân tạo **Amazon Rekognition** để nhận diện vật thể, gắn thẻ nhãn (Tags) tự động và kiểm duyệt nội dung ảnh (Content Moderation), sau đó cập nhật ngược lại các nhãn AI này vào bảng dữ liệu **DynamoDB**.
 

### 4. Triển khai kỹ thuật  
* **Các giai đoạn triển khai:**
  1. *Nghiên cứu và Thiết kế kiến trúc:* Phân tích luồng nghiệp vụ tải ảnh, nghiên cứu cơ chế hoạt động của S3 Presigned URL và thiết kế sơ đồ kiến trúc hướng sự kiện (1 tháng).
  2. *Dự toán chi phí và Phân tích biên an toàn:* Sử dụng công cụ AWS Pricing Calculator để thiết lập hạn mức, cấu hình các Budget Alert ngăn ngừa rủi ro phát sinh vòng lặp vô tận (Tháng 1).
  3. *Phát triển & Cấu hình dịch vụ nền tảng:* Khởi tạo hạ tầng mạng, cấu hình bảng DynamoDB, viết mã nguồn cho các hàm Lambda nén ảnh và tích hợp API AI Rekognition (Tháng 2).
  4. *Tích hợp Frontend, Kiểm thử và Triển khai:* Kết nối giao diện React App hoàn chỉnh, thực hiện các bài kiểm thử stress-test luồng xử lý ảnh đồng thời, dọn dẹp tài nguyên rác và đưa hệ thống lên môi trường Production thực tế (Tháng 3).

* **Yêu cầu kỹ thuật:**
  * *Frontend:* Ứng dụng Single Page Application (React.js/Next.js), sử dụng AWS Amplify SDK hoặc Amazon Cognito Auth SDK để quản lý phiên đăng nhập của người dùng. Tích hợp thư viện Axios hỗ trợ tải luồng dữ liệu nhị phân (Binary stream) trực tiếp lên S3 qua Presigned URL.
  * *Backend & Hạ tầng Cloud:* Mã nguồn Lambda sử dụng ngôn ngữ lập trình Python (thư viện Pillow/Boto3) hoặc Node.js để tối ưu thời gian khởi động lạnh (Cold start). Cấu hình DynamoDB ở chế độ On-Demand lưu trữ linh hoạt. Thiết lập chính sách Origin Access Control (OAC) bảo vệ S3 Bucket khỏi mọi truy cập công khai không qua xác thực.

### 5. Lộ trình & Mốc triển khai  
* **Tháng 1:** Hoàn thành nghiên cứu lý thuyết hạ tầng AWS; Đăng ký tài khoản Free Tier và cấu hình bảo mật cơ bản (IAM, Budgets).
* **Tháng 2:** Hoàn thiện thiết kế kiến trúc hệ thống, cấu hình thành công API Gateway, các bảng cơ sở dữ liệu DynamoDB và viết hoàn chỉnh mã nguồn cho chuỗi Lambda xử lý ảnh ngầm.
* **Tháng 3:** Triển khai xây dựng giao diện người dùng frontend, thực hiện tích hợp kiểm thử end-to-end từ client lên đám mây, viết tài liệu hướng dẫn kỹ thuật trên GitHub và nghiệm thu dự án.

### 6. Ước tính ngân sách  
Chi phí dự toán được tính toán dựa trên quy mô thử nghiệm hệ thống MVP xử lý trung bình dưới 5,000 ảnh/tháng (Nằm trọn trong gói AWS Free Tier vĩnh viễn và 12 tháng đầu):

* **AWS Lambda:** 0.00 USD/tháng (Nằm trong hạn mức 1 triệu requests miễn phí).
* **Amazon S3:** ~0.05 USD/tháng (Lưu trữ ảnh thô và ảnh nén, dưới hạn mức 5 GB miễn phí).
* **Amazon DynamoDB:** 0.00 USD/tháng (Chế độ On-Demand, dưới 25 GB miễn phí dữ liệu dữ liệu tĩnh).
* **Amazon API Gateway:** 0.00 USD/tháng (Dưới 1 triệu requests miễn phí).
* **Amazon Rekognition:** 0.00 USD/tháng (Dưới hạn mức 5,000 ảnh miễn phí/tháng của AWS Free Tier).
* **AWS WAF:** ~5.00 USD/tháng (Chi phí cố định cho Web ACL và các luật cơ bản - Có thể tắt khi làm lab thử nghiệm để tối ưu ngân sách).

*Tổng chi phí ước tính:* ~0.00 USD/tháng (Nếu vận hành hoàn toàn trong Free Tier và tắt AWS WAF khi test nội bộ) hoặc ~5.05 USD/tháng (Nếu bật đầy đủ lá chắn bảo mật WAF).

### 7. Đánh giá rủi ro  
* **Rủi ro vòng lặp vô tận (Infinite Loops):** Lambda xử lý ảnh xong lại lưu đè vào thư mục cũ, kích hoạt chính nó liên tục gây tiêu tốn lượng Credits lớn.
  * *Biện pháp giảm thiểu:* Tách biệt hoàn toàn hai S3 bucket độc lập: `raw-images-bucket` (chỉ bucket này có quyền kích hoạt trigger Lambda) và `processed-images-bucket` (chỉ dùng để chứa ảnh kết quả, không cấu hình trigger).
* **Rủi ro tấn công từ chối dịch vụ (DDoS) hoặc Spam API:** Người dùng bên ngoài gửi liên tục requests giả lập làm tràn hàng triệu Lambda executions.
  * *Biện pháp giảm thiểu:* Đứng trước hệ thống luôn có lớp chắn AWS WAF chặn IP nghi vấn, đồng thời cấu hình Rate Limiting (giới hạn số request/phút trên từng API key) tại API Gateway.
* **Rủi ro rò rỉ dữ liệu S3 công khai:** File ảnh của người dùng bị truy cập trái phép.
  * *Biện pháp giảm thiểu:* Bật tính năng Block Public Access của S3 bucket, chỉ cho phép đọc tệp thông qua CloudFront URLs kết hợp ký xác thực an toàn.

### 8. Kết quả kỳ vọng  
* **Về mặt kỹ thuật:** Hệ thống đạt độ trễ phản hồi tối thiểu, giao diện mượt mà do các tác vụ nặng (nén ảnh, gọi AI bóc tách nhãn) đã được đẩy hoàn toàn xuống tầng ngầm xử lý bất đồng bộ theo thời gian thực.
* **Giá trị dài hạn:** Xây dựng thành công một bộ khung kiến trúc phần mềm đám mây chuẩn Clean Code, có khả năng tự động mở rộng quy mô (Auto-scaling) từ một vài người dùng ban đầu lên đến hàng vạn người dùng đồng thời mà không cần can thiệp cấu hình lại hệ thống quản trị phần cứng.