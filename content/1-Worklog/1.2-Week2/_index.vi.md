---
title: "Worklog Tuần 2"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.2. </b> "
---
### Mục tiêu tuần 2:
* Nắm vững các khái niệm kiến trúc mạng cốt lõi của AWS Virtual Private Cloud (VPC) và nguyên tắc bảo mật mạng.
* Thiết kế và triển khai thực tế mạng VPC tùy chỉnh nhiều tầng bảo mật với các public và private subnet.
* Cấu hình Internet Gateway (IGW), NAT Gateway, Route Tables, Security Groups, Network ACLs và EC2 Instance Connect Endpoint.

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - **Tìm hiểu khái niệm lý thuyết VPC:** <br>&emsp; + Module 02-01 - Tổng quan về AWS Virtual Private Cloud <br>&emsp; + Module 02-02 - Khái niệm bảo mật VPC & tính năng Multi-VPC <br>&emsp; + Module 02-03 - Kết nối mạng nâng cao: VPN, DirectConnect, LoadBalancer | 27/04/2026 | 27/04/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3 | - **Thực hành khởi tạo hạ tầng VPC cốt lõi:** <br>&emsp; + Module 02-Lab03-01 - Khởi tạo Amazon VPC & cấu hình AWS VPN Site-to-Site <br>&emsp; + Module 02-Lab03-01.1 - Thiết kế và phân bổ các Subnet <br>&emsp; + Module 02-Lab03-01.2 - Cấu hình Route Tables định tuyến traffic | 28/04/2026 | 28/04/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4 | - **Thực hành tích hợp Gateway:** <br>&emsp; + Module 02-Lab03-01.3 / Lab03-03.3 - Tạo và gán Internet Gateway (IGW) <br>&emsp; + Module 02-Lab03-01.4 / Lab03-04.3 - Cấu hình NAT Gateway cho Private Subnet truy cập outbound <br>&emsp; + Module 02-Lab03-03.4 - Cấu hình bảng định tuyến Internet | 29/04/2026 | 29/04/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5 | - **Thực hành bảo mật và lọc lưu lượng mạng:** <br>&emsp; + Module 02-Lab03-02.1 / Lab03-03.5 - Cấu hình bảo mật cấp instance với Security Groups <br>&emsp; + Module 02-Lab03-02.2 - Cấu hình tường lửa cấp subnet với Network ACLs | 30/04/2026 | 30/04/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6 | - **Khởi tạo EC2 Instance & kết nối Endpoint:** <br>&emsp; + Module 02-Lab03-04.1 - Tạo EC2 Instance trong các Subnet <br>&emsp; + Module 02-Lab03-04.2 - Kiểm tra kết nối <br>&emsp; + Module 02-Lab03-04.5 - Cấu hình EC2 Instance Connect Endpoint | 01/05/2026 | 01/05/2026 | <https://cloudjourney.awsstudygroup.com/> |

### Kết quả đạt được tuần 2:
* Nắm vững mô hình thiết kế VPC, bao gồm logic định tuyến với Route Tables, Internet Gateway và NAT Gateway.
* Làm chủ các cơ chế bảo mật: phân định rõ ràng giữa Security Groups (stateful) và Network ACLs (stateless).
* Triển khai thành công mạng VPC hoàn chỉnh gồm các Public Subnet và Private Subnet tách biệt.
* Cấu hình và kết nối an toàn tới EC2 Instance qua EC2 Instance Connect Endpoint mà không cần Bastion Host hay Public IP.