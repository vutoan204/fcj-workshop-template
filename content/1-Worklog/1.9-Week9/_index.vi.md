---
title: "Worklog Tuần 9"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.9. </b> "
---



### Mục tiêu tuần 9:
* Hoàn thiện toàn bộ sơ đồ kiến trúc hệ thống cho nền tảng xử lý ảnh thông minh từ tầng Frontend, Edge đến Backend.
* Thiết kế giải pháp bảo mật biên bên ngoài tích hợp Amazon Amplify, tường lửa AWS WAF và bảo vệ API Gateway.
* Làm rõ cơ chế xác thực người dùng Amazon Cognito (AccessToken/IDToken), phân quyền nhóm trong User Pool và luồng kiểm tra token.
* Thống nhất các thành phần kiến trúc và cấu trúc dữ liệu với các thành viên trong nhóm để thiết lập khung làm việc chung.

### Các công việc thực hiện trong tuần:
| Ngày | Nội dung công việc thực tế | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Thiết kế phân khu Frontend trên sơ đồ, liên kết các luồng dữ liệu đi từ Amplify qua WAF để chặn lọc request xấu trước khi đẩy về API Gateway. | 15/06/2026 | 15/06/2026 | <https://aws.amazon.com/architecture/> |
| 3 | - Nghiên cứu và hoàn thiện sơ đồ kết nối các nhánh dịch vụ Backend gồm Lambda, S3, DynamoDB và tích hợp thêm cấu trúc nhận diện của Amazon Rekognition. | 16/06/2026 | 16/06/2026 | <https://aws.amazon.com/architecture/> |
| 4 | - Tìm hiểu kỹ các quy tắc cấu hình bộ lọc mã độc của AWS WAF và đối chiếu tài liệu nén/tối ưu hóa tài nguyên tĩnh khi phân phối qua Amplify. <br> - Cấu hình tường lửa AWS WAF (Web Application Firewall) đứng chặn trực tiếp trước API Gateway để ngăn chặn các lỗ hổng bảo mật web phổ biến. | 17/06/2026 | 17/06/2026 | <https://000026.awsstudygroup.com/vi/> |
| 5 | - Đọc tài liệu của Amazon Cognito để làm rõ cơ chế sinh, kiểm tra Token (AccessToken/IDToken) và phân tách nhóm người dùng trong User Pool. <br> - Khởi tạo và cấu hình Amazon Cognito User Pool, thiết lập các trường thuộc tính tùy chỉnh (Custom Attributes) và quy tắc xác thực mật khẩu an toàn. | 18/06/2026 | 18/06/2026 | <https://000141.awsstudygroup.com/vi/> |
| 6 | - Thảo luận với thành viên nhóm phụ trách Frontend để giải thích cặn kẽ các thành phần, luồng đi của dữ liệu trên sơ đồ để các bạn nắm được hướng gọi API. | 19/06/2026 | 19/06/2026 |  |

### Kết quả đạt được tuần 9:
* Sơ đồ kiến trúc tổng thể của hệ thống đã được vẽ xong, chuẩn hóa 100% luồng kết nối và được cả nhóm thông qua.
* Nắm chắc giải pháp dựng hàng rào bảo mật vòng ngoài (WAF/Amplify) và cách thức quản lý danh tính bằng Cognito để áp dụng cho giai đoạn thiết kế API.
* Làm rõ toàn bộ thắc mắc về mặt kiến trúc cho các thành viên, giúp đội Frontend có bộ khung chuẩn để bắt tay vào làm.
