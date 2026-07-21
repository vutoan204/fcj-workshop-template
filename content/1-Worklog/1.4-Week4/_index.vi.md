---
title: "Worklog Tuần 4"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.4. </b> "
---
### Mục tiêu tuần 4:
* Tìm hiểu sâu các tính năng tính toán EC2 bao gồm Instance Types, AMI, EBS, Instance Store, User Data, Metadata và Auto Scaling.
* Triển khai chiến lược sao lưu tự động bằng AWS Backup.
* Thiết kế môi trường lưu trữ lai qua AWS Storage Gateway kết nối server on-premises tới Amazon S3.

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - **Nghiên cứu lý thuyết EC2 nâng cao:** <br>&emsp; + Module 03-01 - Compute VM trên AWS <br>&emsp; + Module 03-01-01 - Instance type <br>&emsp; + Module 03-01-02 - AMI / Backup / Key Pair <br>&emsp; + Module 03-01-03 - Elastic Block Store (EBS) | 11/05/2026 | 11/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3 | - **Nghiên cứu lý thuyết EC2 nâng cao (Tiếp theo):** <br>&emsp; + Module 03-01-04 - Instance store <br>&emsp; + Module 03-01-05 - User data <br>&emsp; + Module 03-01-06 - Metadata <br>&emsp; + Module 03-01-07 - EC2 Auto Scaling <br>&emsp; + Module-03-02 - EC2 Auto Scaling - EFS/FSx - Lightsail - MGN | 12/05/2026 | 12/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4 | - **Thực hành Lab AWS Backup:** <br>&emsp; + Module 03-Lab13-01 - Triển khai AWS Backup - Giới thiệu <br>&emsp; + Module 03-Lab13-02.2 - Triển khai hạ tầng <br>&emsp; + Module 03-Lab13-03 - Tạo Kế hoạch Backup <br>&emsp; + Module 03-Lab13-05 - Kiểm tra Khôi phục <br>&emsp; + Module 03-Lab13-06 - Dọn dẹp tài nguyên | 13/05/2026 | 13/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5 | - **Thực hành Lab Storage Gateway:** <br>&emsp; + Module 03-Lab24-01.1 - Tạo S3 Bucket <br>&emsp; + Module 03-Lab24-01.2 - Tạo EC2 làm Storage Gateway | 14/05/2026 | 14/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6 | - **Thực hành Lab Storage Gateway (Tiếp theo):** <br>&emsp; + Module 03-Lab24-02.1 - Tạo Storage Gateway <br>&emsp; + Module 03-Lab24-02.2 - Tạo File Shares | 15/05/2026 | 15/05/2026 | <https://cloudjourney.awsstudygroup.com/> |

### Kết quả đạt được tuần 4:
* Hiểu rõ các dòng EC2 instance và phân biệt bộ nhớ lưu trữ bền vững EBS với bộ nhớ tạm thời tốc độ cao Instance Store.
* Cấu hình User Data script tự động cài đặt ứng dụng khi khởi tạo EC2 và truy xuất thông tin metadata qua CLI.
* Thiết lập thành công các Kế hoạch Backup tự động và khôi phục dữ liệu bằng AWS Backup.
* Xây dựng hệ thống File Gateway lưu trữ lai gắn ổ đĩa từ server nội bộ vào S3 Bucket.