---
title: "Worklog Tuần 10"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 1.10. </b> "
---



### Mục tiêu tuần 10:
* Nghiên cứu kiến trúc Endpoint REST API Gateway bảo mật và xây dựng cơ chế tự động phân quyền truy cập người dùng với Cognito Authorizer.
* Triển khai giải pháp cấp quyền tải ảnh trực tiếp lên S3 Bucket thông qua S3 Presigned URL có thời hạn, tránh lộ thông tin lưu trữ.
* Tối ưu hóa tài nguyên tính toán serverless với AWS Lambda và xây dựng ma trận phân quyền IAM để giới hạn tối đa đặc quyền hàm.
* Tổng hợp chi tiết logic xử lý Backend và quy tắc xác thực Token để tạo nền tảng phát triển vững chắc cho cả nhóm.

### Các công việc thực hiện trong tuần:
| Ngày | Nội dung công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Nghiên cứu tài liệu API Gateway để lên danh sách các đường dẫn REST API, định hình cấu trúc dữ liệu JSON đầu vào/đầu ra cho các đầu việc xử lý ảnh. | 22/06/2026 | 22/06/2026 | <https://docs.aws.amazon.com/apigateway/> |
| 3 | - Tìm hiểu giải pháp tối ưu tài nguyên tính toán với AWS Lambda (Serverless) và xây dựng ma trận phân quyền IAM để giới hạn tối đa quyền hạn của các hàm. | 23/06/2026 | 23/06/2026 | <https://000022.awsstudygroup.com/vi/> |
| 4 | - Tìm hiểu giải pháp lưu trữ đối tượng Amazon S3, phân lớp dữ liệu nguội S3 Glacier.<br> - Nghiên cứu các cơ chế bảo mật nâng cao: Versioning, Lifecycle policies, Bucket policies trên S3. | 24/06/2026 | 24/06/2026 | https://000078.awsstudygroup.com/vi/ |
| 5 | - Tổng hợp lại tài liệu hướng dẫn logic kiểm tra Token tự động thông qua API Gateway Authorizer trước khi request được chuyển tiếp xuống Backend. | 25/06/2026 | 25/06/2026 | <https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-use-lambda-authorizer.html> |
| 6 | - Hỗ trợ thành viên cấu hình code Backend làm rõ cơ chế tích hợp chuỗi ký Pre-signed URL vào hàm Lambda và cách xử lý khi Token từ Client gửi lên bị hết hạn. | 26/06/2026 | 26/06/2026 |  |

### Kết quả đạt được tuần 10:

* Thiết kế hoàn chỉnh kiến trúc cổng kết nối REST API bảo mật, có cơ chế tự động thẩm định quyền hạn truy cập của người dùng.
* Xây dựng xong giải pháp kỹ thuật cấp quyền upload ảnh trực tiếp lên kho chứa thông qua chuỗi Pre-signed URL, giảm thiểu rủi ro lộ lọt thông tin.
* Tổng hợp bộ tài liệu hướng dẫn logic xử lý Backend chi tiết, phục vụ làm căn cứ phát triển phần mềm cho nhóm.

