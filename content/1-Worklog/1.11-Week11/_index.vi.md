---
title: "Worklog Tuần 11"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 1.11. </b> "
---



### Mục tiêu tuần 11:

* Xác định cấu trúc lưu trữ dữ liệu tệp phân tầng kết hợp thiết kế mô hình bảng NoSQL, đồng thời đánh giá tính khả thi của luồng tự động xử lý ảnh dựa trên sự kiện.

### Các công việc thực hiện trong tuần:
| Ngày | Nội dung công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Nghiên cứu giải pháp phân chia kho lưu trữ Object Storage thành 2 vùng biệt lập (vùng chứa ảnh gốc và vùng chứa ảnh nén) cùng cấu hình chặn truy cập chéo CORS. | 29/06/2026 | 29/06/2026 | <https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html> |
| 3 | - Lên phương án cấu hình bảng NoSQL trên DynamoDB, chọn khóa chính (Partition Key) và lập các chỉ mục phụ nhằm tối ưu tốc độ tìm kiếm siêu dữ liệu ảnh. | 30/06/2026 | 30/06/2026 | <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.CoreComponents.html> |
| 4 | - Nghiên cứu tài liệu AWS để ứng dụng tính năng S3 Event Notification, thiết kế luồng tự động gọi Lambda xử lý ngay khi có tệp mới xuất hiện ở kho ảnh gốc. | 01/07/2026 | 01/07/2026 | <https://docs.aws.amazon.com/AmazonS3/latest/userguide/NotificationHowTo.html> |
| 5 | - Tiến hành kiểm thử trên mặt lý thuyết về luồng kích hoạt hướng sự kiện, rà soát tính hợp lệ của cấu trúc file dữ liệu mẫu gửi qua lại giữa các dịch vụ. | 02/07/2026 | 02/07/2026 |  |
| 6 | - Nghiên cứu tính năng DynamoDB Streams nhằm bắt trọn các sự kiện thay đổi dữ liệu theo thời gian thực và đẩy tín hiệu sang một hàm Lambda phân tích độc lập <br>&emsp; + Đọc tài liệu API của Amazon Rekognition và nghiên cứu kiến trúc phân phối của mạng lưới CDN CloudFront. | 03/07/2026 | 03/07/2026 | <https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Streams.html> <https://docs.aws.amazon.com/rekognition/latest/dg/labels.html> <https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Introduction.html> |

### Kết quả đạt được tuần 11:

* Hoàn thành xong hồ sơ thiết kế kho lưu trữ tệp phân tầng và sơ đồ cấu trúc dữ liệu NoSQL cho DynamoDB, đảm bảo tốc độ truy vấn tốt.
* Xác thực tính khả thi của mô hình kiến trúc hướng sự kiện (Event-Driven), đảm bảo hệ thống tự động chạy ngầm mà không cần máy chủ giám sát.
.


