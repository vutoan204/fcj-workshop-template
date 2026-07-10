---
title: "Worklog Tuần 5"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.5. </b> "
---
### Mục tiêu Tuần 5:
* Tìm hiểu và triển khai phân giải Hybrid DNS giữa môi trường Cloud và On-premises thông qua Route 53 Resolver.
* Kết nối tới RDP qua RDGW, kiểm tra kết quả phân giải DNS, và thực hiện dọn dẹp tài nguyên lab.

### Các tác vụ cần thực hiện trong tuần này:
| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - **Khởi tạo Hạ tầng Hybrid DNS:** <br>&emsp; + Module 02-Lab10-01 - Thiết lập Hybrid DNS với Route 53 Resolver (Giới thiệu) <br>&emsp; + Module 02-Lab10-02.1 - Tạo Key Pair <br>&emsp; + Module 02-Lab10-02.2 - Khởi tạo CloudFormation Template | 18/05/2026 | 18/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3 | - **Cấu hình Bảo mật & Kết nối RDGW:** <br>&emsp; + Module 02-Lab10-02.3 - Cấu hình Security Group <br>&emsp; + Module 02-Lab10-03 - Kết nối tới RDGW | 19/05/2026 | 19/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4 | - **Cấu hình Tích hợp DNS Hướng ra ngoài (Outbound):** <br>&emsp; + Module 02-Lab10-05 - Thiết lập DNS <br>&emsp; + Module 02-Lab10-05.1 - Tạo Route 53 Outbound Endpoint | 20/05/2026 | 20/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5 | - **Cấu hình Resolver Rules & Inbound Endpoints:** <br>&emsp; + Module 02-Lab10-05.2 - Tạo Route 53 Resolver Rules <br>&emsp; + Module 02-Lab10-05.3 - Tạo Route 53 Inbound Endpoints | 21/05/2026 | 21/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6 | - **Xác minh và Dọn dẹp Tài nguyên:** <br>&emsp; + Module 02-Lab10-05.4 - Kiểm tra kết quả và phân giải domain hybrid <br>&emsp; + Module 02-Lab10-06 - Dọn dẹp tài nguyên để tối ưu hóa chi phí | 22/05/2026 | 22/05/2026 | <https://cloudjourney.awsstudygroup.com/> |

### Thành tựu Tuần 5:
* Sử dụng AWS CloudFormation để tự động khởi tạo hạ tầng cho môi trường lab Hybrid DNS.
* Tạo thành công giải pháp Hybrid DNS có khả năng phân giải tên miền giữa On-Premises và AWS Cloud thông qua Route 53 Resolver Inbound/Outbound Endpoints & Rules.
* Thiết lập kết nối Remote Desktop Gateway (RDGW) thành công.
* Dọn dẹp toàn bộ tài nguyên để giảm thiểu chi phí phát sinh ở mức thấp nhất.


