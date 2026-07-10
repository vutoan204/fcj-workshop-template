---
title: "Worklog Tuần 9"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.9. </b> "
---



### Mục tiêu tuần 9:

* Tập trung chốt hạ sơ đồ kiến trúc tổng thể cho toàn bộ hệ thống xử lý ảnh và làm rõ phương án phân phối giao diện kèm cơ chế xác thực người dùng ở vòng ngoài.

### Các công việc thực hiện trong tuần:
| Ngày | Nội dung công việc thực tế | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | Phác thảo và hoàn thiện phân khu Frontend trên sơ đồ; liên kết các luồng dữ liệu đi từ Amplify qua WAF để chặn lọc request xấu trước khi đẩy về API Gateway. | 15/06/2026 | 15/06/2026 | <https://docs.aws.amazon.com/amplify/> |
| 3 | Vẽ nốt nửa còn lại của sơ đồ, kết nối các nhánh dịch vụ Backend gồm Lambda, S3, DynamoDB và tích hợp thêm cấu trúc nhận diện của Amazon Rekognition. | 16/06/2026 | 16/06/2026 | <https://aws.amazon.com/architecture/> |
| 4 | Tìm hiểu kỹ các quy tắc cấu hình bộ lọc mã độc của AWS WAF và đối chiếu tài liệu nén/tối ưu hóa tài nguyên tĩnh khi phân phối qua Amplify. | 17/06/2026 | 17/06/2026 | <https://docs.aws.amazon.com/waf/> |
| 5 | Đọc tài liệu của Amazon Cognito để làm rõ cơ chế sinh, kiểm tra Token (AccessToken/IDToken) và phân tách nhóm người dùng trong User Pool. | 18/06/2026 | 18/06/2026 | <https://docs.aws.amazon.com/cognito/> |
| 6 | Thảo luận với thành viên nhóm phụ trách Frontend để giải thích cặn kẽ các thành phần, luồng đi của dữ liệu trên sơ đồ để các bạn nắm được hướng gọi API. | 19/06/2026 | 19/06/2026 |  |

### Kết quả đạt được tuần 9:
* Sơ đồ kiến trúc tổng thể của hệ thống đã được vẽ xong, chuẩn hóa 100% luồng kết nối và được cả nhóm thông qua.
* Nắm chắc giải pháp dựng hàng rào bảo mật vòng ngoài (WAF/Amplify) và cách thức quản lý danh tính bằng Cognito để áp dụng cho giai đoạn thiết kế API.
* Làm rõ toàn bộ thắc mắc về mặt kiến trúc cho các thành viên, giúp đội Frontend có bộ khung chuẩn để bắt tay vào làm.
