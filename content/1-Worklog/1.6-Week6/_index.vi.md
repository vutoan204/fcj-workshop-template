---
title: "Worklog Tuần 6"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.6. </b> "
---
### Mục tiêu Tuần 6:
* Tìm hiểu và nắm vững chi tiết các tính năng tính toán nâng cao của EC2, bao gồm Instance Types, AMI, EBS, Instance Store, User Data, Metadata, và Auto Scaling.
* Triển khai các chính sách sao lưu tự động bằng cách sử dụng AWS Backup.
* Thiết kế môi trường lưu trữ hybrid thông qua AWS Storage Gateway kết nối các máy chủ on-premises với S3.

### Các tác vụ cần thực hiện trong tuần này:
| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - **Nghiên cứu sâu về EC2:** <br>&emsp; + Module 03-01 - Compute VM trên AWS <br>&emsp; + Module 03-01-01 - Instance type <br>&emsp; + Module 03-01-02 - AMI / Backup / Key Pair <br>&emsp; + Module 03-01-03 - Elastic block store | 25/05/2026 | 25/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3 | - **Nghiên cứu sâu về EC2 (Tiếp tục):** <br>&emsp; + Module 03-01-04 - Instance store <br>&emsp; + Module 03-01-05 - User data <br>&emsp; + Module 03-01-06 - Meta data <br>&emsp; + Module 03-01-07 - EC2 auto scaling <br>&emsp; + Module-03-02 - EC2 Autoscaling - EFS/FSx - Lightsail - MGN | 26/05/2026 | 26/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4 | - **Thực hành Lab AWS Backup:** <br>&emsp; + Module 03-Lab13-01 - Triển khai AWS Backup - Giới thiệu <br>&emsp; + Module 03-Lab13-02.2 - Triển khai hạ tầng <br>&emsp; + Module 03-Lab13-03 - Tạo Backup plan <br>&emsp; + Module 03-Lab13-05 - Kiểm tra khôi phục (Restore) <br>&emsp; + Module 03-Lab13-06 - Dọn dẹp tài nguyên | 27/05/2026 | 27/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5 | - **Thực hành Lab Storage Gateway:** <br>&emsp; + Module 03-Lab24-01.1 - Tạo S3 Bucket <br>&emsp; + Module 03-Lab24-01.2 - Tạo EC2 cho Storage Gateway | 28/05/2026 | 28/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6 | - **Thực hành Lab Storage Gateway (Tiếp tục):** <br>&emsp; + Module 03-Lab24-02.1 - Tạo Storage Gateway <br>&emsp; + Module 03-Lab24-02.2 - Tạo File Shares | 29/05/2026 | 29/05/2026 | <https://cloudjourney.awsstudygroup.com/> |

### Thành tựu Tuần 6:
* Tiếp thu kiến thức chuyên sâu về các loại EC2 instance (tối ưu hóa tính toán, tối ưu hóa bộ nhớ, tối ưu hóa lưu trữ) và phân biệt giữa các ổ đĩa lưu trữ bền vững EBS với ổ đĩa tạm thời tốc độ cao Instance Store.
* Cấu hình các đoạn mã lệnh User Data để tự động chạy các tác vụ khởi động và truy xuất thành công dữ liệu metadata của instance qua CLI.
* Thiết lập thành công các Kế hoạch Sao lưu (Backup Plans) tự động và xác thực quá trình khôi phục hệ thống bằng AWS Backup.
* Xây dựng thành công hệ thống File Gateway lai (hybrid) giúp ánh xạ các ổ đĩa mạng nội bộ on-premises tới các S3 bucket.

