---
title: "Workshop"
date: 2024-01-01
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Workshop Smart Image Platform

Workshop gồm hai phương thức triển khai/tham khảo và một phần trình diễn kết quả:

1. **Triển khai bằng AWS CDK:** luồng chính, phản ánh cấu hình trong dự án `AWS-Project` và là nguồn chuẩn khi có khác biệt.
2. **Tham khảo trên AWS Console (tùy chọn):** minh họa vị trí và ý nghĩa của các cấu hình mà CDK tạo. Các bước này có thể được giản lược và không nên thực hiện lại trong cùng tài khoản/Region nếu stack CDK đã tồn tại.
3. **Giới thiệu và xác minh sản phẩm:** trình bày deployment trên domain thực, các tính năng quan sát được và cách đối chiếu với tài nguyên AWS.

Các ví dụ và ảnh chụp sử dụng Region `ap-southeast-1` và môi trường `staging`. Tên vật lý có thể chứa hậu tố do CDK hoặc CloudFormation tạo.

> **Quan trọng:** Không trộn tài nguyên tạo thủ công với tài nguyên do CDK quản lý trong cùng môi trường. CDK không tự quản lý hoặc xóa các tài nguyên được tạo riêng trên Console.

## Nội dung

1. [Tổng quan và kiến trúc](5.1-Workshop-overview/)
2. [Điều kiện tiên quyết](5.2-Prerequisites/)
3. [Phần I – Triển khai bằng AWS CDK](5.3-CDK-Deployment/)
4. [Phần II – Storage và Database trên Console (tùy chọn)](5.7-Storage-Database/)
5. [Cognito trên Console (tùy chọn)](5.4-Cognito-Auth/)
6. [Backend Serverless trên Console (tùy chọn)](5.5-Backend-Serverless/)
7. [Amplify Hosting trên Console (tùy chọn)](5.6-Frontend-Amplify/)
8. [Giới thiệu sản phẩm trên domain thực](5.8-Product-Showcase/)
9. [Kiểm thử và xác minh](5.9-Testing-Validation/)
10. [Dọn dẹp tài nguyên](5.8-Cleanup/)
