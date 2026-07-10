---
title: "Worklog Tuần 7"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.7. </b> "
---
### Mục tiêu Tuần 7:
* Thiết lập một trang web tĩnh có độ bảo mật cao trên S3 kết hợp với mạng phân phối nội dung Amazon CloudFront CDN.
* Tìm hiểu và cấu hình tính năng S3 Bucket Versioning (Quản lý phiên bản) và Cross-Region Replication (CRR - Sao chép liên vùng) để hỗ trợ các kế hoạch khôi phục sau thảm họa (DR).

### Các tác vụ cần thực hiện trong tuần này:
| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - **Triển khai S3 Static Website:** <br>&emsp; + Module 03-Lab57-02.1 - Tạo S3 bucket <br>&emsp; + Module 03-Lab57-02.2 - Tải dữ liệu lên <br>&emsp; + Module 03-Lab57-03 - Bật tính năng static website | 01/06/2026 | 01/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3 | - **Cấu hình Quyền truy cập Công khai S3 (Public Access):** <br>&emsp; + Module 03-Lab57-04 - Cấu hình chặn truy cập công khai (public access block) <br>&emsp; + Module 03-Lab57-05 - Cấu hình các đối tượng công khai (public objects) <br>&emsp; + Module 03-Lab57-06 - Kiểm tra trang web | 02/06/2026 | 02/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4 | - **Cấu hình Tích hợp CloudFront CDN:** <br>&emsp; + Module 03-Lab57-07.1 - Chặn toàn bộ quyền truy cập công khai <br>&emsp; + Module 03-Lab57-07.2 - Cấu hình Amazon CloudFront <br>&emsp; + Module 03-Lab57-07.3 - Kiểm tra Amazon Cloudfront | 03/06/2026 | 03/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5 | - **Triển khai Bảo vệ Dữ liệu & Sao chép (Replication):** <br>&emsp; + Module 03-Lab57-08 - Bucket Versioning <br>&emsp; + Module 03-Lab57-09 - Di chuyển các đối tượng (objects) | 04/06/2026 | 04/06/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6 | - **Hoàn tất Sao chép Liên vùng & Dọn dẹp Tài nguyên:** <br>&emsp; + Module 03-Lab57-10 - Sao chép đối tượng liên vùng (multi Region) <br>&emsp; + Module 03-Lab57-11 - Dọn dẹp tài nguyên | 05/06/2026 | 05/06/2026 | <https://cloudjourney.awsstudygroup.com/> |

### Thành tựu Tuần 7:
* Triển khai thành công trang web tĩnh lưu trữ trên S3, giới hạn lưu lượng truy cập công khai trực tiếp vào bucket, và định tuyến các yêu cầu qua CloudFront CDN nhằm bảo vệ dữ liệu và tăng tốc độ tải trang trên toàn cầu.
* Triển khai S3 Bucket Versioning để lưu trữ các bản sao tệp tin cũ và theo dõi lịch sử thay đổi.
* Thiết lập thành công tính năng Sao chép Liên vùng (Cross-Region Replication - CRR) để tự động đồng bộ hóa các tệp tin sang một vùng thứ hai, hỗ trợ các chiến lược khôi phục sau thảm họa (DR) mạnh mẽ.