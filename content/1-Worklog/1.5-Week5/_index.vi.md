---
title: "Worklog Tuần 5"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.5. </b> "
---
### Mục tiêu tuần 5:
* Cấu hình website tĩnh an toàn trên Amazon S3 kết hợp mạng phân phối nội dung Amazon CloudFront CDN.
* Cấu hình chính sách chặn truy cập công khai S3 (Public Access Block) và Bucket Policy để đảm bảo an toàn dữ liệu.
* Tìm hiểu và cấu hình S3 Bucket Versioning và Cross-Region Replication (CRR) hỗ trợ kế hoạch khắc phục sự cố (DR).

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - **Triển khai S3 Static Website:** <br>&emsp; + Module 03-Lab57-02.1 - Tạo S3 bucket <br>&emsp; + Module 03-Lab57-02.2 - Tải dữ liệu <br>&emsp; + Module 03-Lab57-03 - Bật tính năng static website | 18/05/2026 | 18/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3 | - **Cấu hình Quyền truy cập Public S3:** <br>&emsp; + Module 03-Lab57-04 - Cấu hình chặn truy cập public <br>&emsp; + Module 03-Lab57-05 - Cấu hình các đối tượng public <br>&emsp; + Module 03-Lab57-06 - Kiểm tra website | 19/05/2026 | 19/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4 | - **Cấu hình tích hợp CloudFront CDN:** <br>&emsp; + Module 03-Lab57-07.1 - Chặn toàn bộ truy cập public <br>&emsp; + Module 03-Lab57-07.2 - Cấu hình Amazon CloudFront <br>&emsp; + Module 03-Lab57-07.3 - Kiểm tra Amazon Cloudfront | 20/05/2026 | 20/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5 | - **Triển khai Bảo vệ dữ liệu & Nhân bản:** <br>&emsp; + Module 03-Lab57-08 - Bucket Versioning <br>&emsp; + Module 03-Lab57-09 - Chuyển đổi đối tượng | 21/05/2026 | 21/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6 | - **Hoàn tất Nhân bản Multi-Region & Dọn dẹp:** <br>&emsp; + Module 03-Lab57-10 - Nhân bản đối tượng đa vùng <br>&emsp; + Module 03-Lab57-11 - Dọn dẹp tài nguyên | 22/05/2026 | 22/05/2026 | <https://cloudjourney.awsstudygroup.com/> |

### Kết quả đạt được tuần 5:
* Triển khai website tĩnh trên S3, chặn truy cập trực tiếp vào bucket và điều hướng qua CloudFront CDN để bảo mật và tăng tốc truy cập toàn cầu.
* Áp dụng S3 Bucket Versioning để quản lý các phiên bản tệp tin cũ.
* Thiết lập Cross-Region Replication (CRR) tự động sao chép dữ liệu sang vùng phụ (secondary region) phục vụ chiến lược khắc phục thảm họa (Disaster Recovery).
