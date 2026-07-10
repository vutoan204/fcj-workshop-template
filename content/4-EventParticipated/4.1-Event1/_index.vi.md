---
title: "AWS Community Day Vietnam 2026"
date: 2026-05-23
weight: 1
chapter: false
pre: " <b> 4.1. </b> "
---



# Bài thu hoạch: AWS Community Day Vietnam 2026

### Thông Tin Sự Kiện
* **Tên sự kiện:** AWS Community Day Vietnam 2026
* **Ngày & Thời gian:** Ngày 23 tháng 05 năm 2026 (08:00 - 17:00)
* **Địa điểm:** Thành phố Hồ Chí Minh, Việt Nam
* **Vai trò:** Người tham dự

---

### Mục Tiêu Của Sự Kiện
AWS Community Day là một hội nghị công nghệ quy mô lớn được tổ chức bởi cộng đồng người dùng AWS User Group Vietnam. Các mục tiêu chính của sự kiện bao gồm:
* Chia sẻ các xu hướng công nghệ mới nhất trong hệ sinh thái đám mây AWS, đặc biệt là Generative AI (GenAI), kiến trúc Serverless và thiết kế ứng dụng hiện đại.
* Kết nối các kỹ sư phần mềm, Kiến trúc sư giải pháp (SA), AWS Community Builders và cộng đồng doanh nghiệp công nghệ tại Việt Nam.
* Chia sẻ các kinh nghiệm thực tế (best practices), các nghiên cứu điển hình về chuyển đổi số và chiến lược tối ưu hóa chi phí đám mây từ các doanh nghiệp hàng đầu (GoTymeX, VPBank, Cloud Kinetics...).

---

### Các Bài Tham Luận Nổi Bật

#### 1. Context Is Everything - Making AI Actually Work for You
* **Diễn giả:** Tinh Truong (Platform Engineer, GoTymeX)
* **Nội dung chính:**
  * Chỉ ra nguyên nhân cốt lõi khiến các ứng dụng AI thường xuyên bị ảo giác (hallucination) không hẳn là do LLM yếu, mà là do thiếu ngữ cảnh (Context) liên quan và cụ thể.
  * Giới thiệu kiến trúc "Prompt to Memory Pipeline" – trình bày cách xây dựng các luồng nạp ngữ cảnh động và giải pháp lưu trữ bộ nhớ dài hạn cho AI.
  * Chia sẻ kinh nghiệm làm việc hiệu quả cùng các trợ lý AI và lộ trình phát triển kỹ năng cho sinh viên/lập trình viên trẻ trong kỷ nguyên trí tuệ nhân tạo.

#### 2. Friendly AI Assistant w/ Amazon Quick
* **Diễn giả:** Pham Ngoc Hai Anh (G-AsiaPacific Vietnam, AWS Community Builder)
* **Nội dung chính:**
  * Phân tích điểm nghẽn của doanh nghiệp: Người dùng phi kỹ thuật mất nhiều thời gian tra cứu dữ liệu phân mảnh từ nhiều hệ thống khác nhau.
  * Giới thiệu trợ lý AI "Amazon Quick" được xây dựng trên Amazon Bedrock, tích hợp hơn 40 cổng kết nối cơ sở dữ liệu bảo mật và khả năng tìm kiếm web thông minh.
  * Case Study thực tế: Trợ lý quản lý dự án giúp tự động tạo biên bản họp (MoM), soạn thảo email nháp và theo dõi lịch trình của các lập trình viên.

#### 3. From Edge to Origin: CloudFront as Your Foundation
* **Diễn giả:** Nguyen Tuan Thinh (DevOps Engineer, First Cloud AI Journey)
* **Nội dung chính:**
  * Giải quyết vấn đề chi phí không thể dự đoán trước của các mô hình giá CDN truyền thống (trả theo lưu lượng).
  * Đề xuất mô hình CDN giá cố định sử dụng Amazon CloudFront, AWS WAF, Route 53 và tài nguyên lưu trữ S3 để quản lý ngân sách hàng tháng ổn định.
  * Chi tiết các cấu hình nâng cao: Bảo mật tại biên (AWS Shield, WAF, Mutual TLS, geo-blocking), thiết lập Origin Failover dự phòng, hỗ trợ HTTP/3 (QUIC) và tùy biến logic tại biên bằng CloudFront Functions hoặc Lambda@Edge.

#### 4. 36 hrs with LotusHacks: Building UTMorpho from Idea to Reality
* **Nhóm diễn giả:** UTMorpho Team (Thành viên tham dự LotusHacks 2026)
* **Nội dung chính:**
  * Chia sẻ hành trình thiết kế và xây dựng ứng dụng Generative Design "UTMorpho" trong một cuộc thi hackathon kéo dài 36 giờ.
  * Kiến trúc hệ thống: Frontend React SPA chạy trên CloudFront + S3; Backend API Gateway + Lambda ghi nhận dữ liệu vào S3 và DynamoDB; engine GenAI Bedrock phân tích các bản vẽ phác thảo, tạo giao diện UI và truyền trực tuyến (stream) bản xem trước React động.
  * Các bài học về xử lý AI sinh quá mức (overgeneration), giới hạn token và áp lực thời gian cực hạn.

#### 5. Non-Determinism of 'Deterministic' LLM Settings
* **Diễn giả:** Duc Dao (Solution Architect, Cloud Kinetics)
* **Nội dung chính:**
  * Giải thích hiện tượng mặc dù đã cấu hình tham số `temperature = 0` trên các API LLM thương mại, kết quả trả về của mô hình vẫn không mang tính xác định hoàn toàn (non-deterministic).
  * Làm rõ nguyên nhân kỹ thuật: Phép toán dấu phẩy động không kết hợp (non-associative) trên GPU khi chạy song song và cơ chế batching yêu cầu động trên các nền tảng suy luận dùng chung.
  * Chia sẻ chiến lược giảm thiểu: Biểu quyết đa số (Majority Voting), tự triển khai mô hình với giới hạn runtime xác định và ràng buộc cấu trúc đầu ra (JSON Mode, Regex Grammar).

#### 6. Enterprise-Grade Multi-Agent System: The Case of Startup Credit Scoring
* **Diễn giả:** Vy Lam (Senior Business Systems Analyst, VPBank)
* **Nội dung chính:**
  * Giải quyết vấn đề thiếu hụt dữ liệu tài chính truyền thống khi đánh giá điểm tín dụng cho các doanh nghiệp khởi nghiệp (startups).
  * Chỉ ra hạn chế của mô hình đơn trợ lý (single-agent): Nghẽn ngữ cảnh, tính kiểm toán thấp và tỷ lệ thất bại cao trong các quyết định phức tạp.
  * Đề xuất khung hội đồng ảo sử dụng hệ thống Multi-Agent (các agent chuyên biệt về tài chính, rủi ro, phân tích thị trường) để đạt được sự đồng thuận.
  * Kết quả kinh doanh: Rút ngắn thời gian xử lý hồ sơ tín dụng từ vài tuần xuống còn vài giờ (nhanh hơn 95%), giảm 95% số giờ làm việc thủ công của chuyên viên phân tích và dự phóng tỷ lệ hoàn vốn (ROI) rất cao.

---

### Những Gì Học Được & Bài Học Rút Ra

#### 1. Tư Duy Thiết Kế Kiến Trúc
* **Bảo mật và Tối ưu hóa tại Biên:** CloudFront không chỉ đơn thuần là cache tài nguyên mà còn đóng vai trò là lớp phòng thủ biên thiết yếu. Thiết lập S3 Gateway Endpoints và cấu hình tối ưu gói truyền CDN giúp tiết kiệm đáng kể chi phí truyền tải ra ngoài mạng (egress network fees).
* **Nhu cầu nghiệp vụ đi trước:** Việc triển khai công nghệ, đặc biệt là tích hợp AI, phải phục vụ trực tiếp cho nhu cầu vận hành thực tế của doanh nghiệp để tạo ra ROI thiết thực, thay vì chỉ chạy theo xu hướng.

#### 2. Khái Niệm GenAI Nâng Cao
* **Kiểm soát ràng buộc của LLM:** Hiểu rõ tính không xác định của LLM giúp các lập trình viên backend xây dựng các cơ chế phòng ngừa lỗi và ngữ pháp cấu trúc đầu ra chặt chẽ, tránh tin tưởng tuyệt đối vào kết quả thô của AI.
* **Hệ thống Multi-Agent:** Chuyển đổi từ chatbot đơn lẻ sang hệ thống Multi-Agent cộng tác là chìa khóa để giải quyết các bài toán nghiệp vụ phức tạp của doanh nghiệp.

---

### Ứng Dụng Vào Dự Án NodeJ2Car
Các kiến thức thu hoạch từ sự kiện đã được áp dụng trực tiếp vào việc thiết kế và triển khai hệ thống backend **NodeJ2Car**:
1. **Edge Security & CDN:** Triển khai lưu trữ Frontend React trên S3 và phân phối qua CloudFront với chứng chỉ bảo mật SSL/TLS từ ACM cùng tường lửa AWS WAF tại biên (hoàn thành ở Tuần 12).
2. **Xử lý bất đồng bộ phi tập trung:** Áp dụng hàng đợi tin nhắn Amazon SQS (triển khai ở Tuần 8) để đệm và xử lý các webhook hóa đơn cổng thanh toán, bảo vệ cụm Express chính khỏi việc quá tải khi có lượng truy cập đột biến.
3. **Tối ưu hóa API Quét Ảnh AI:** Áp dụng cơ chế làm sạch tên tệp/siêu dữ liệu và các ràng buộc dữ liệu đầu vào regex trên API quét ảnh phụ tùng AI (triển khai ở Tuần 9) để giảm thiểu tỷ lệ phân loại sai từ AI.

---

### Một Số Hình Ảnh Khi Tham Gia Sự Kiện
![AWS Community Day Vietnam 2026](/images/4-EventParticipated/4.1-Event1/event.jpg)

---

### Trải Nghiệm Cá Nhân
> AWS Community Day Vietnam 2026 mang lại bầu không khí công nghệ rất sôi động và truyền cảm hứng. Việc học hỏi kinh nghiệm từ các diễn giả đầu ngành đã giúp tôi mở rộng tầm nhìn kỹ thuật và tạo dựng những kết nối chất lượng trong cộng đồng. Đây là động lực lớn cho quá trình thực tập của tôi.
