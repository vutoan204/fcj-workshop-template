---
title: "Worklog Tuần 8"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.8. </b> "
---
### Mục tiêu Tuần 8:
* Triển khai lưu trữ tham số và cấu hình ứng dụng an toàn với AWS Systems Manager Parameter Store và AWS Secrets Manager kèm tính năng tự động xoay vòng khóa.
* Đánh giá tổng quan các chứng chỉ tuân thủ quốc tế (ISO 27001, PCI DSS) và khảo sát cơ chế phát hiện bất thường của Amazon GuardDuty.
* So sánh giải pháp quản lý khóa mã hóa AWS KMS với thiết bị phần cứng AWS CloudHSM, quy trình cấp phát SSL/TLS qua AWS Certificate Manager (ACM).
* Phân tích giải pháp SSO và quản lý phân quyền người dùng qua Amazon Cognito, AWS IAM Identity Center và AWS Directory Service.
* Rà soát lỗ hổng bảo mật với AWS Inspector, tối ưu chi phí và an toàn với Trusted Advisor, thực hành viết Lambda với chuỗi kết nối mã hóa.

### Các tác vụ cần thực hiện trong tuần này:
| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2 | - Tìm hiểu lưu trữ tham số/cấu hình ứng dụng an toàn với AWS Systems Manager Parameter Store.<br> - Giải pháp bảo mật thông tin kết nối Cơ sở dữ liệu (Core DB Credentials) và tính năng tự động xoay vòng khóa (Automatic Rotation) bằng AWS Secrets Manager. | 08/06/2026 | 08/06/2026 | https://000031.awsstudygroup.com/vi/ |
| 3 | - Đánh giá tổng quan các chứng chỉ tuân thủ quốc tế (ISO 27001, PCI DSS).<br> - Khảo sát Amazon GuardDuty: Cơ chế ứng dụng Machine Learning để phân tích nhật ký (log analysis) và phát hiện bất thường. | 09/06/2026 | 09/06/2026 | https://000098.awsstudygroup.com/vi/ |
| 4 | - So sánh giải pháp quản lý khóa mã hóa AWS KMS (mã hóa dữ liệu tĩnh - Data at rest) và thiết bị bảo mật phần cứng AWS CloudHSM.<br> - Quy trình cấp phát, cấu hình và tự động gia hạn SSL/TLS Cert cho HTTPS qua AWS Certificate Manager (ACM). | 10/06/2026 | 10/06/2026 | https://000033.awsstudygroup.com/vi/ |
| 5 | - Phân tích giải pháp SSO và quản lý phân quyền người dùng ứng dụng qua Amazon Cognito.<br> - Tìm hiểu mô hình xác thực tập trung cho doanh nghiệp kết hợp AWS IAM Identity Center (SSO) và AWS Directory Service. | 11/06/2026 | 11/06/2026 | https://000141.awsstudygroup.com/vi/ |
| 6 | - Rà soát lỗ hổng bảo mật hạ tầng/ứng dụng bằng AWS Inspector, tối ưu chi phí & an toàn với Trusted Advisor, xuất chứng nhận qua AWS Artifact.<br> - Lab thực hành: Cấu hình Parameter Store/Secrets Manager để mã hóa chuỗi kết nối (Connection String) và gọi an toàn từ hàm AWS Lambda. | 12/06/2026 | 12/06/2026 |  https://000031.awsstudygroup.com/vi/3-patchmanager/ |

### Thành tựu Tuần 8:
 - Triển khai giải pháp lưu trữ tham số an toàn với SSM Parameter Store và quản lý thông tin kết nối Cơ sở dữ liệu với tính năng tự động xoay vòng khóa (Automatic Rotation) bằng AWS Secrets Manager.
 - Phân tích giải pháp SSO, tích hợp xác thực người dùng qua Amazon Cognito và làm chủ mô hình phân quyền doanh nghiệp kết hợp AWS IAM Identity Center và AWS Directory Service.
 - Rà soát lỗ hổng bằng AWS Inspector, phát hiện bất thường với Amazon GuardDuty, tối ưu chi phí qua Trusted Advisor và tự động hóa quy trình cấp phát SSL/TLS Cert qua ACM.