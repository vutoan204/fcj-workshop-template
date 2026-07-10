---
title: "Blog 3"
date: 2024-01-01
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# VÌ SAO KHÔNG NÊN MỞ SSH PORT 22 CHO EC2? TÌM HIỂU AWS SYSTEMS MANAGER SESSION MANAGER

Khi mới học AWS, nhiều người thường truy cập EC2 bằng cách mở port 22 trong Security Group, tạo SSH key pair rồi SSH trực tiếp vào server.

Cách này quen thuộc và vẫn dùng được, nhưng trong môi trường production nó có một số hạn chế và rủi ro:
* **Quản lý SSH Key phức tạp:** Phải cấp phát, lưu trữ và thu hồi file private key cho từng người dùng, đồng thời phải thực hiện rotate key định kỳ.
* **Kiểm soát IP khó khăn:** Phải thường xuyên cập nhật Security Group để whitelist IP động của các lập trình viên.
* **Cần Bastion Host:** Nếu EC2 nằm trong private subnet, bạn bắt buộc phải dựng và quản trị thêm Bastion Host để làm máy trung chuyển.
* **Tăng Attack Surface (Bề mặt tấn công):** Việc public port 22 ra Internet sẽ tạo cơ hội cho kẻ tấn công thực hiện brute-force và khai thác lỗ hổng.

AWS Systems Manager Session Manager là một cách tiếp cận an toàn hơn để truy cập EC2. Với Session Manager, admin có thể mở phiên làm việc (shell session) với EC2 thông qua AWS Console hoặc AWS CLI mà không cần mở inbound port, không cần dựng Bastion Host và không cần quản lý SSH key theo cách truyền thống.

---

### Ưu điểm nổi bật của AWS Systems Manager Session Manager

1. **Quản lý truy cập tập trung qua IAM:** Thay vì chia sẻ file private key, admin có thể cấp hoặc thu hồi quyền truy cập bằng các IAM policy. Điều này giúp kiểm soát truy cập chặt chẽ hơn và tuân thủ nguyên lý least privilege.
2. **Không mở cổng inbound:** Hoạt động thông qua kết nối outbound từ instance đến các endpoint của Systems Manager, do đó không cần cấu hình mở cổng inbound port 22 trên Security Group.
3. **Không cần Bastion Host:** Hỗ trợ kết nối trực tiếp đến các EC2 instance nằm trong private subnet.
4. **Audit Logs chi tiết:** Lưu trữ nhật ký hoạt động và nội dung các câu lệnh thực thi vào Amazon CloudWatch Logs hoặc Amazon S3 để phục vụ cho việc kiểm tra bảo mật.

---

### Điều kiện để sử dụng Session Manager

Để sử dụng Session Manager đúng cách, EC2 cần đáp ứng các điều kiện sau:
* **SSM Agent:** EC2 instance phải được cài đặt SSM Agent (đã được cài sẵn mặc định trên các bản AMI phổ biến của AWS như Amazon Linux 2/2023).
* **IAM Role:** EC2 instance cần được gắn IAM Role/Instance Profile có quyền làm việc với Systems Manager (ví dụ policy `AmazonSSMManagedInstanceCore`).
* **Kết nối Outbound:** Instance cần có khả năng kết nối outbound HTTPS port 443 tới các endpoint của Systems Manager.
* **Subnet riêng tư:** Nếu EC2 nằm trong private subnet không có Internet hay NAT Gateway, có thể thiết lập VPC Endpoint (AWS PrivateLink) để kết nối riêng tư và an toàn đến Systems Manager.

---

### Các kiểu kết nối và lưu ý

Session Manager hỗ trợ nhiều hình thức kết nối:
* **Standard Session (Phiên tiêu chuẩn):** Người dùng tương tác trực tiếp qua console/CLI mà không cần SSH key. Toàn bộ nội dung lệnh thực thi sẽ được ghi log đầy đủ.
* **SSH Tunneling / Port Forwarding (Đường truyền SSH / Chuyển tiếp cổng):** Session Manager hoạt động như một tunnel bảo mật (ví dụ để SSH qua Session Manager hoặc port forwarding truy cập DB). Ở chế độ này, việc ghi nhật ký lệnh nội bộ sẽ bị giới hạn.

### Kết luận

Trong cloud, bảo mật không chỉ là cấu hình tường lửa hay dùng key mạnh. Đôi khi cách tốt nhất là không public cổng quản trị ra Internet ngay từ đầu. Sử dụng Session Manager là một pattern vô cùng đáng học và áp dụng khi vận hành AWS ở quy mô thực tế.

### Sơ đồ kiến trúc
![Sơ đồ hoạt động Systems Manager Session Manager](/images/3-BlogsPosted/blog3.png)

### Facebook Post & Tài liệu tham khảo
* **Bài đăng gốc trên Facebook:** [AWS Study Group Post #2207318586699768](https://www.facebook.com/groups/awsstudygroupfcj/permalink/2207318586699768/)
* **Link tham khảo:**
  1. [What is AWS Systems Manager?](https://docs.aws.amazon.com/systems-manager/latest/userguide/what-is-systems-manager.html)
  2. [Step 6: (Optional) Use AWS PrivateLink to set up a VPC endpoint for Session Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager-access-control-vpc-endpoints.html)
  3. [AWS Systems Manager Session Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager.html)