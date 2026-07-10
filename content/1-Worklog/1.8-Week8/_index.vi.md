---
title: "Worklog Tuần 8"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.8. </b> "
---
### Mục tiêu Tuần 8:

* Nghiên cứu sâu các giải pháp lưu trữ nâng cao của AWS (S3 Access Points, Storage Classes, Glacier, Snow Family) và thực hành di chuyển máy chủ on-premises sử dụng VM Import/Export.
* Triển khai hệ thống tệp chia sẻ hiệu năng cao sử dụng Multi-AZ FSx cho Windows File Server (SSD/HDD FSx, Shadow copies, Quotas, Data deduplication) và AWS Storage Gateway.
* Học hỏi các khái niệm bảo mật cốt lõi của AWS (Shared Responsibility Model, IAM, Cognito, AWS Organizations, Identity Center, KMS, Security Hub).
* Thực hành quản lý tài nguyên bằng AWS Tags và tự động hóa vận hành máy chủ (Lambda tự động start/stop EC2 dựa trên tag kèm thông báo qua Slack).
* Triển khai kiểm soát truy cập chi tiết với IAM Policies/Roles, Switch Role giới hạn theo điều kiện IP/Thời gian, và mã hóa KMS kết hợp kiểm toán bằng AWS CloudTrail cùng truy vấn log qua Amazon Athena.

### Các tác vụ cần thực hiện trong tuần này:
| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - **Lưu trữ Nâng cao & Di cư Hạ tầng:** <br>&emsp; + Module 04-01 -> 04-04: Lý thuyết Cốt lõi về Lưu trữ AWS <br>&emsp; + Module 04-Lab14-01 -> Lab14-05: VM Import/Export di chuyển các VM on-premises lên AWS <br>&emsp; + Module 04-Lab13-02.1 -> Lab13-06: Cấu hình AWS Backup nâng cao | 08/06/2026 | 08/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3 | - **Triển khai Hệ thống Tệp cho Doanh nghiệp:** <br>&emsp; + Module 04-Lab24-2.1 -> Lab24-3: Storage Gateway và gắn (mount) các File Shares cục bộ <br>&emsp; + Module 04-Lab25-2.2 -> Lab25-13: Cấu hình Multi-AZ FSx Windows File Server, shadow copies, quotas, và chống trùng lặp dữ liệu (deduplication) <br>&emsp; + Module 04-Lab57-2.1 -> Lab57-11: Thiết lập S3 Static Website & CloudFront nâng cao | 09/06/2026 | 09/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4 | - **Bảo mật Hạ tầng & Tự động hóa Vận hành:** <br>&emsp; + Module 05-01 -> 05-08: Lý thuyết Cốt lõi về Bảo mật AWS <br>&emsp; + Module 05-Lab18-02 -> Lab18-04: Đánh giá tuân thủ bảo mật sử dụng AWS Security Hub <br>&emsp; + Module 05-Lab22-2.1 -> Lab22-7: Phát triển AWS Lambda để tự động start/stop EC2 dựa trên tag kèm thông báo Slack Webhook | 10/06/2026 | 10/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5 | - **Quản lý Tài nguyên & Phân quyền IAM Nâng cao:** <br>&emsp; + Module 05-Lab27-2.1.1 -> Lab27-4: Quản lý Resource Tags & Resource Group <br>&emsp; + Module 05-Lab28-2.1 -> Lab28-6: Thực thi IAM Policy, Role, và Switch Roles <br>&emsp; + Module 05-Lab30-3 -> Lab30-6: Tạo tài khoản người dùng hạn chế (Limited User) và kiểm tra các chính sách truy cập <br>&emsp; + Module 05-Lab44-2 -> Lab44-5: Giới hạn quyền Switch Role dựa trên Source IP và khung giờ cố định | 11/06/2026 | 11/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6 | - **Mã hóa Dữ liệu & Kiểm toán Hoạt động:** <br>&emsp; + Module 05-Lab33-2.1 -> Lab33-7: Mã hóa AWS KMS cho S3, ghi log hoạt động với AWS CloudTrail, và truy vấn log qua Amazon Athena <br>&emsp; + Module 05-Lab48-1.1 -> Lab48-4: Phân biệt Access Keys với IAM Instance Profiles (IAM Roles) trên các instance EC2 | 12/06/2026 | 12/06/2026 | <https://cloudjourney.awsstudygroup.com/> |

### Thành tựu Tuần 8:

* Di chuyển thành công các máy ảo từ môi trường ảo hóa cục bộ (On-premises) để chạy trực tiếp trên AWS Cloud thông qua quy trình VM Import/Export.
* Xây dựng hệ thống lưu trữ tệp chia sẻ cấp doanh nghiệp sử dụng FSx cho Windows File Server, kích hoạt tính năng Data Deduplication để tối ưu hóa không gian đĩa (tiết kiệm tới 50-60%) và cấu hình Shadow Copies giúp người dùng tự khôi phục tệp nhanh chóng.
* Tự động hóa việc vận hành các instance EC2 ngoài giờ làm việc bằng cách sử dụng hàm AWS Lambda được kích hoạt bởi Amazon EventBridge Rules dựa trên các Resource Tags cụ thể, kết hợp gửi cập nhật trạng thái tức thời đến kênh Slack.
* Đánh giá và kiểm toán các chỉ số tuân thủ bảo mật của tài khoản AWS dựa trên các tiêu chuẩn thực hành tốt nhất trong ngành bằng AWS Security Hub.
* Triển khai quản trị IAM nâng cao bằng cách định nghĩa các điều kiện nghiêm ngặt (condition keys) trong IAM Policies nhằm giới hạn hoạt động Switch Role dựa trên địa chỉ IP nguồn và khung giờ làm việc được phép của người dùng.
* Bảo mật dữ liệu lưu trữ (data at rest) bên trong Amazon S3 sử dụng các khóa AWS KMS do khách hàng quản lý, ghi lại toàn bộ hoạt động API hệ thống qua AWS CloudTrail, và thực hiện phân tích tuân thủ log kiểm toán mượt mà bằng các câu lệnh SQL thuần túy ngay trong Amazon Athena.
* Làm chủ các thực hành bảo mật cốt lõi tốt nhất: sử dụng IAM Roles gán cho các instance EC2 (IAM Instance Profiles) thay vì mã hóa cứng (hardcode) các cặp Access Key/Secret Key tĩnh bên trong mã nguồn hoặc môi trường ứng dụng.