---
title: "Worklog Tuần 4"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.4. </b> "
---
### Week 4 Objectives:
* Hoàn thành triển khai mạng VPC, cấu hình định tuyến internet qua NAT Gateway & IGW, và thiết lập các Security Groups.
* Khởi chạy các instance EC2 trong các subnet và cấu hình các phương thức kết nối an toàn sử dụng EC2 Instance Connect Endpoint.

### Tasks to be carried out this week:
| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - **Cấu hình VPC Routing & Security:** <br>&emsp; + Module 02-Lab03-03.3 - Tạo Internet Gateway <br>&emsp; + Module 02-Lab03-03.4 - Tạo Route Table cho Định tuyến Internet Hướng ra ngoài qua Internet Gateway <br>&emsp; + Module 02-Lab03-03.5 - Tạo các security groups | 08/05/2026 | 08/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3 | - **Khởi chạy các Instance & Cấu hình NAT Routing:** <br>&emsp; + Module 02-Lab03-04.1 - Tạo các EC2 Instance trong các Subnet <br>&emsp; + Module 02-Lab03-04.2 - Kiểm tra kết nối | 09/05/2026 | 09/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4 | - **Khởi chạy các Instance & Cấu hình NAT Routing (Tiếp tục):** <br>&emsp; + Module 02-Lab03-04.3 - Tạo NAT Gateway <br>&emsp; + Module 02-Lab03-04.5 - EC2 Instance Connect Endpoint | 10/05/2026 | 10/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5 | - **Xem xét và Tối ưu hóa Sơ đồ Mạng (Network Topology):** <br>&emsp; + Xác minh các chính sách định tuyến giữa public subnet và private subnet <br>&emsp; + Kiểm tra các giao thức cô lập lưu lượng truy cập inbound và outbound | 11/05/2026 | 11/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6 | - **Đánh giá Bảo mật của Mạng Cốt lõi:** <br>&emsp; + Kiểm tra các quy tắc inbound/outbound cho các Security Group mới tạo <br>&emsp; + Xác minh các kết nối an toàn sử dụng EC2 Instance Connect Endpoint | 12/05/2026 | 12/05/2026 | <https://cloudjourney.awsstudygroup.com/> |

### Week 4 Achievements:
* Triển khai thành công cấu hình định tuyến VPC đầy đủ chức năng, cho phép các public subnet truy cập Internet qua IGW và các private subnet truy cập qua NAT Gateway.
* Khởi chạy thành công các instance EC2 bên trong nhiều subnet của VPC.
* Cấu hình và sử dụng thành công EC2 Instance Connect Endpoint để thiết lập các phiên SSH/RDP an toàn mà không cần IP public hoặc Bastion Host.