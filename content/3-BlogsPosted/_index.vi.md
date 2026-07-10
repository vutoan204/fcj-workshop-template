---
title: "Các bài blogs đã đăng"
date: 2024-01-01
weight: 3
chapter: false
pre: " <b> 3. </b> "
---



Tại đây sẽ là phần liệt kê, giới thiệu các blogs mà các bạn đã đăng trên [AWS Study Group](https://www.facebook.com/groups/awsstudygroupfcj). Ví dụ:

###  [Blog 1 - TỐI ƯU HÓA HÌNH ẢNH HIỆU QUẢ VỚI AMAZON CLOUDFRONT VÀ AWS LAMBDA](3.1-Blog1/)
Kiến trúc này sử dụng Amazon CloudFront và AWS Lambda để cung cấp giải pháp tối ưu hóa hình ảnh hiệu quả, tức thời. Bằng cách chỉ lưu trữ một hình ảnh gốc chất lượng cao duy nhất trong Amazon S3, hệ thống sẽ tự động nén, thay đổi kích thước và chuyển đổi hình ảnh sang các định dạng thế hệ mới như WebP hoặc AVIF chỉ khi người dùng yêu cầu. Quá trình xử lý không cần máy chủ, theo yêu cầu này giúp loại bỏ chi phí lưu trữ không cần thiết cho các biến thể hình ảnh được tạo sẵn. Hơn nữa, các tài sản được tối ưu hóa được lưu vào bộ nhớ đệm tại các vị trí biên của CloudFront, giúp giảm đáng kể độ trễ, băng thông truyền dữ liệu và cải thiện các chỉ số hiệu suất web như Core Web Vitals của Google.

###  [Blog 2 -GIẢI PHÁP XỬ LÝ HÌNH ẢNH DYNAMIC TRÊN AWS VỚI AMAZON SAGEMAKER VÀ AMAZON BEDROCK](3.2-Blog2/)
Kiến trúc lai này kết hợp Amazon SageMaker và Amazon Bedrock để xây dựng một công cụ chỉnh sửa hình ảnh AI tạo sinh hiệu quả về chi phí và hiệu suất cao. Đối với phân đoạn đối tượng và điền ngữ cảnh, các mô hình mã nguồn mở như SAM và LaMa của Meta được lưu trữ cục bộ trên SageMaker Multi-Model Endpoints (MME), tối ưu hóa việc chia sẻ bộ nhớ GPU thông qua NVIDIA Triton Server. Đối với việc điền văn bản nâng cao và xóa khung vẽ, hệ thống chuyển sang sử dụng API Bedrock không máy chủ, gọi các mô hình nền tảng mạnh mẽ như SDXL 1.0 và Amazon Titan Image Generator. Cách tiếp cận này tạo ra sự cân bằng lý tưởng cho các doanh nghiệp bằng cách duy trì độ trễ thấp khi thực thi các quy trình cốt lõi trong khi vẫn khai thác được các mô hình tạo sinh mạnh mẽ hoàn toàn theo yêu cầu.

###  [Blog 3 - VÌ SAO KHÔNG NÊN MỞ SSH PORT 22 CHO EC2? TÌM HIỂU AWS SYSTEMS MANAGER SESSION MANAGER](3.3-Blog3/)
Kiến trúc này nhấn mạnh vai trò của AWS Systems Manager Session Manager như một giải pháp thay thế an toàn cho việc mở cổng SSH truyền thống (cổng 22) trên các phiên bản EC2. Bằng cách dựa vào các kết nối HTTPS đi ra từ AWS Systems Manager, nó loại bỏ hoàn toàn các rủi ro bảo mật của các cổng quản trị bị lộ, sự phức tạp của việc thiết lập danh sách trắng IP công cộng và nhu cầu về máy chủ Bastion. Việc kiểm soát truy cập được tập trung hóa bằng cách sử dụng các chính sách IAM tiêu chuẩn thay vì quản lý khóa SSH tĩnh rườm rà, đảm bảo nguyên tắc quyền hạn tối thiểu nghiêm ngặt. Hệ thống yêu cầu một SSM Agent đang hoạt động và các vai trò thực thi IAM phù hợp trên phiên bản để thiết lập các phiên shell an toàn. Ngoài ra, nó cung cấp nhật ký kiểm toán toàn diện bằng cách tự động ghi nhật ký tất cả các lệnh đã thực thi trực tiếp vào Amazon CloudWatch hoặc Amazon S3.