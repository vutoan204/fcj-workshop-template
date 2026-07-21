---
title: "Worklog Tuần 6"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.6. </b> "
---
### Mục tiêu tuần 6:
* Nghiên cứu giải pháp lưu trữ nâng cao (Glacier, Snow Family) và thực hành di chuyển hệ thống VM Import/Export từ On-premises lên AWS.
* Triển khai lưu trữ doanh nghiệp Multi-AZ FSx cho Windows File Server (Deduplication, Shadow copies) và đánh giá an toàn thông tin bằng Security Hub.
* Tự động hóa vận hành EC2 qua Lambda theo Tag phát thông báo Slack, cấu hình phân quyền IAM Switch Role nâng cao kèm điều kiện và mã hóa dữ liệu KMS kết hợp CloudTrail/Athena.

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - **Lưu trữ nâng cao & Chuyển dịch hạ tầng:** <br>&emsp; + Module 04-01 -> 04-04: Lý thuyết cốt lõi lưu trữ AWS <br>&emsp; + Module 04-Lab14-01 -> Lab14-05: VM Import/Export di chuyển máy ảo từ On-premises lên AWS | 25/05/2026 | 25/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3 | - **Triển khai Hệ thống tệp tin doanh nghiệp:** <br>&emsp; + Module 04-Lab25-2.2 -> Lab25-13: Cấu hình Multi-AZ FSx Windows File Server, shadow copies, quotas và deduplication | 26/05/2026 | 26/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4 | - **Bảo mật hạ tầng & Tự động hóa vận hành:** <br>&emsp; + Module 05-01 -> 05-08: Lý thuyết bảo mật cốt lõi AWS <br>&emsp; + Module 05-Lab18-02 -> Lab18-04: Đánh giá tuân thủ bảo mật với AWS Security Hub <br>&emsp; + Module 05-Lab22-2.1 -> Lab22-7: Viết AWS Lambda tự động start/stop EC2 theo Tag kèm thông báo Slack Webhook | 27/05/2026 | 27/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5 | - **Quản lý tài nguyên & Phân quyền IAM nâng cao:** <br>&emsp; + Module 05-Lab27-2.1.1 -> Lab27-4: Quản lý Resource Tags & Resource Group <br>&emsp; + Module 05-Lab28-2.1 -> Lab28-6: Thực thi IAM Policy, Role và Switch Roles <br>&emsp; + Module 05-Lab44-2 -> Lab44-5: Giới hạn quyền Switch Role dựa theo IP nguồn và khung giờ truy cập | 28/05/2026 | 28/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6 | - **Mã hóa dữ liệu & Kiểm toán hoạt động:** <br>&emsp; + Module 05-Lab33-2.1 -> Lab33-7: Mã hóa KMS cho S3, ghi nhật ký với CloudTrail và truy vấn log qua Amazon Athena <br>&emsp; + Module 05-Lab48-1.1 -> Lab48-4: Phân biệt Access Keys và IAM Instance Profiles (IAM Roles) trên EC2 | 29/05/2026 | 29/05/2026 | <https://cloudjourney.awsstudygroup.com/> |

### Kết quả đạt được tuần 6:
* Di chuyển thành công máy ảo từ môi trường ảo hóa nội bộ (On-premises) lên chạy trực tiếp trên AWS qua quy trình VM Import/Export.
* Xây dựng hệ thống lưu trữ tệp chia sẻ doanh nghiệp FSx cho Windows File Server tối ưu dung lượng đĩa (Deduplication) và khôi phục nhanh (Shadow Copies).
* Tự động hóa bật/tắt EC2 ngoài giờ làm việc bằng AWS Lambda dựa trên Resource Tags kèm thông báo thời gian thực qua kênh Slack.
* Đánh giá tuân thủ an toàn thông tin tài khoản qua AWS Security Hub và áp dụng điều kiện giới hạn Switch Role theo IP và thời gian.
* Mã hóa dữ liệu lưu trữ S3 bằng AWS KMS, ghi nhật ký API với AWS CloudTrail và truy vấn kiểm toán bằng câu lệnh SQL trên Amazon Athena.
