---
title: "Worklog Tuần 11"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 1.11. </b> "
---



### Mục tiêu tuần 11:
* Thiết kế phân tầng lưu trữ tệp tin (bucket ảnh gốc và bucket ảnh đã xử lý) và cấu hình bảng NoSQL trên Amazon DynamoDB với các chỉ mục phụ.
* Xác minh tính khả thi của luồng xử lý ảnh tự động dựa trên sự kiện (Event-Driven) sử dụng S3 Event Notifications để kích hoạt Lambda.
* Triển khai DynamoDB Streams để bắt các sự kiện thay đổi dữ liệu thời gian thực và kích hoạt hàm Lambda phân tích AI bất đồng bộ.
* Nghiên cứu tài liệu Amazon Rekognition AI API để nhận diện vật thể và trích xuất nhãn tag, cùng chiến lược phân phối qua CloudFront CDN.

### Các công việc thực hiện trong tuần:
| Ngày | Nội dung công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Nghiên cứu giải pháp phân chia kho lưu trữ Object Storage thành 2 vùng biệt lập (vùng chứa ảnh gốc và vùng chứa ảnh nén) cùng cấu hình chặn truy cập chéo CORS. | 29/06/2026 | 29/06/2026 | <https://000057.awsstudygroup.com/vi/> |
| 3 | - Lên phương án cấu hình bảng NoSQL trên DynamoDB, chọn khóa chính (Partition Key) và lập các chỉ mục phụ nhằm tối ưu tốc độ tìm kiếm siêu dữ liệu ảnh. <br> - Nghiên cứu tư duy thiết kế khóa (Partition Key, Sort Key) và cơ chế tự động mở rộng quy mô. | 30/06/2026 | 30/06/2026 | <https://000060.awsstudygroup.com/vi/> |
| 4 | - Nghiên cứu tài liệu AWS để ứng dụng tính năng S3 Event Notification, thiết kế luồng tự động gọi Lambda xử lý ngay khi có tệp mới xuất hiện ở kho ảnh gốc. <br> - Tìm hiểu giải pháp lưu trữ đối tượng Amazon S3, phân lớp dữ liệu nguội S3 Glacier. <br> - Cấu hình chính sách bảo mật Bucket Policy và thiết lập Lifecycle policy chuyển đổi dữ liệu tự động | 01/07/2026 | 01/07/2026 | <https://000078.awsstudygroup.com/vi/> <https://000078.awsstudygroup.com/vi/2-resize-image-function/2-2-create-s3-bucket/>|
| 5 | - Tiến hành kiểm thử trên mặt lý thuyết về luồng kích hoạt hướng sự kiện, rà soát tính hợp lệ của cấu trúc file dữ liệu mẫu gửi qua lại giữa các dịch vụ. | 02/07/2026 | 02/07/2026 |  |
| 6 | - Nghiên cứu tính năng DynamoDB Streams nhằm bắt trọn các sự kiện thay đổi dữ liệu theo thời gian thực và đẩy tín hiệu sang một hàm Lambda phân tích độc lập  | 03/07/2026 | 03/07/2026 | <https://000039.awsstudygroup.com/vi/>  |

### Kết quả đạt được tuần 11:

* Hoàn thành xong hồ sơ thiết kế kho lưu trữ tệp phân tầng và sơ đồ cấu trúc dữ liệu NoSQL cho DynamoDB, đảm bảo tốc độ truy vấn tốt.
* Xác thực tính khả thi của mô hình kiến trúc hướng sự kiện (Event-Driven), đảm bảo hệ thống tự động chạy ngầm mà không cần máy chủ giám sát.
.


