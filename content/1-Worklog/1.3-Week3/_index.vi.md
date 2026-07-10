---
title: "Worklog Tuần 3"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.3. </b> "
---
### Mục tiêu Tuần 3:
* Nghiên cứu các khái niệm kiến trúc cốt lõi và cấu hình bảo mật mạng của đám mây riêng ảo AWS Virtual Private Cloud (VPC).
* Thiết kế và triển khai thực tế một hệ thống mạng VPC tùy chỉnh an toàn, phân lớp rõ ràng bao gồm các dải mạng con công khai (Public subnets) và nội bộ (Private subnets).

### Các công việc thực hiện trong tuần này:
| Ngày | Nội dung công việc | Ngày bắt đầu | Ngày hoàn thành | Tài liệu tham khảo |
| --- | --- | --- | --- | --- |
| 2 | - **Nghiên cứu các khái niệm cơ bản về VPC:** <br>&emsp; + Module 02-01 - Tổng quan về AWS Virtual Private Cloud <br>&emsp; + Module 02-02 - Các tính năng bảo mật VPC thiết yếu và kiến trúc đa vùng mạng (Multi-VPC) | 01/05/2026 | 01/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3 | - **Nghiên cứu kiến trúc hạ tầng mạng VPC nâng cao:** <br>&emsp; + Module 02-03 - Kết nối mạng chuyên sâu: VPN, DirectConnect, LoadBalancer và các tài nguyên bổ trợ | 02/05/2026 | 02/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4 | - **Thực hành xây dựng hệ thống mạng VPC (Hạ tầng cơ sở):** <br>&emsp; + Module 02-Lab03-01 - Khởi động mạng Amazon VPC và thiết lập cấu hình AWS VPN Site-to-Site <br>&emsp; + Module 02-Lab03-01.1 - Thiết kế và phân chia các dải mạng con (Subnets) <br>&emsp; + Module 02-Lab03-01.2 - Thiết lập bảng định tuyến để điều phối lưu lượng (Route Tables) | 03/05/2026 | 03/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5 | - **Thực hành xây dựng hệ thống mạng VPC (Tích hợp Cổng kết nối):** <br>&emsp; + Module 02-Lab03-01.3 - Khởi tạo và gắn cổng Internet Gateway (IGW) <br>&emsp; + Module 02-Lab03-01.4 - Thiết lập cổng NAT Gateway để cấp quyền truy cập internet hướng ra ngoài cho mạng nội bộ | 04/05/2026 | 04/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6 | - **Thực hành cấu hình bảo mật và hoàn thiện mạng VPC:** <br>&emsp; + Module 02-Lab03-02.1 - Cấu hình bộ lọc bảo mật có trạng thái (stateful) ở cấp độ máy chủ qua Security Groups <br>&emsp; + Module 02-Lab03-02.2 - Thiết lập tường lửa phi trạng thái (stateless) ở cấp độ dải mạng con qua Network ACLs <br>&emsp; + Module 02-Lab03-03.1 - Thực hành khởi tạo mạng VPC tùy chỉnh trên thực tế <br>&emsp; + Module 02-Lab03-03.2 - Kiểm tra và xác thực cuối cùng đối với cấu hình định tuyến của Subnet | 05/05/2026 | 05/05/2026 | <https://cloudjourney.awsstudygroup.com/> |

### Kết quả đạt được trong Tuần 3:
* Tiếp thu nền tảng vững chắc về mô hình thiết kế VPC, bao gồm logic định tuyến dữ liệu thông qua Route Tables, NAT Gateways và Internet Gateways.
* Làm chủ các cơ chế bảo mật hạ tầng chuyên sâu: ứng dụng hiệu quả các quy tắc tường lửa có trạng thái (Security Groups) để bảo vệ máy chủ độc lập và bộ lọc lưu lượng phi trạng thái (Network ACLs) ở ranh giới các dải mạng con.
* Tự tay xây dựng, thiết lập sơ đồ và cấu hình thành công một mô hình mạng VPC tùy chỉnh phân lớp hoàn chỉnh, bao gồm các phân khu công khai hướng internet (Public subnets) và các phân khu nội bộ chứa backend (Private subnets) trực tiếp trên môi trường quản lý AWS Console.