---
title: "Console - Xác thực Cognito"
date: 2024-01-01
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

# Amazon Cognito trên AWS Console (tùy chọn)

> Bỏ qua bước tạo User Pool nếu Auth stack đã được deploy. Console có thể thay đổi bố cục; cần giữ đúng giá trị cấu hình thay vì phụ thuộc tên nút.

## 1. Tạo User Pool và SPA app client

AWS có thể hiển thị wizard rút gọn hoặc wizard nhiều bước. Hai luồng dưới đây tạo cùng một cấu hình; chỉ thực hiện một luồng.

### A. Giao diện thiết lập nhanh

1. Mở [Amazon Cognito Console](https://console.aws.amazon.com/cognito/) ở Region `ap-southeast-1`.
2. Chọn **Create user pool** hoặc **Set up resources for your application**.
3. Ở **Application type**, chọn **Single-page application (SPA)**.
4. Đặt tên ứng dụng `SmartImage-WebClient-staging`.
5. Ở **Options for sign-in identifiers**, chỉ chọn **Email**; bỏ Username và Phone number.
6. Bật **Enable self-registration**.
7. Ở **Required attributes for sign-up**, chọn `email` và `name`.
8. Không cần return URL hoặc managed login cho workshop vì frontend dùng form React và Amplify Auth trực tiếp.
9. Chọn **Create user directory**.

### B. Giao diện cấu hình nhiều bước

1. Ở **Configure sign-in experience**, chọn **Email** làm sign-in option duy nhất.
2. Ở **Configure security requirements**, đặt password policy tối thiểu 8 ký tự và yêu cầu chữ hoa, chữ thường, số, ký tự đặc biệt.
3. Chọn MFA **Optional** và chỉ bật **Authenticator apps/TOTP**; không bật SMS để khớp CDK.
4. Ở **Configure sign-up experience**, bật self-service sign-up, yêu cầu `email` và `name`, đồng thời bật tự động xác minh email.
5. Đặt account recovery chỉ qua **Verified email**.
6. Ở **Configure message delivery**, có thể chọn **Send email with Cognito** cho lab. Cấu hình SES riêng chỉ cần thiết khi triển khai production.
7. Ở **Integrate app**, nhập User Pool name `SmartImage-UserPool-staging`.
8. Tạo app client `SmartImage-WebClient-staging` loại public client, chọn **Don't generate a client secret** và bật luồng SRP (`ALLOW_USER_SRP_AUTH`).
9. Không bật Hosted UI/managed login nếu frontend tiếp tục dùng form tùy biến.
10. Kiểm tra trang review rồi chọn **Create user pool**.

![Cấu hình SPA, email và thuộc tính đăng ký](/images/5-Workshop/5.4-Cognito-Auth/cognito_userpool_setup.png)

## 2. Custom attribute

CDK hiện tạo `custom:role` kiểu String, mutable, độ dài 1–20. Có thể tạo thuộc tính này để mô phỏng đúng stack, nhưng ứng dụng không dùng nó làm nguồn phân quyền chính.

Nếu wizard cho phép khai báo custom attribute:

1. Mở User Pool vừa tạo và tìm **Sign-up experience** hoặc **Sign-up**.
2. Trong **Custom attributes**, chọn **Add custom attribute**.
3. Nhập attribute name `role`, kiểu **String**.
4. Đặt minimum length `1`, maximum length `20` và cho phép thay đổi giá trị (**Mutable**).
5. Chọn **Save changes** hoặc **Create**.

Custom attribute thường không thể xóa hoặc thay đổi kiểu sau khi User Pool được tạo. Nếu Console hiện tại yêu cầu khai báo attribute ngay trong wizard, quay lại bước tạo pool thay vì tạo thêm một pool không cần thiết.

> **Khác biệt cần lưu ý:** Frontend và backend kiểm tra claim `cognito:groups`, không kiểm tra `custom:role`. Thuộc tính này có thể được xem là tùy chọn đối với phần Console.

## 3. User groups

1. Mở User Pool và chọn tab **User groups**.
2. Chọn **Create group**.
3. Nhập group name `admin`, description `Administrators with moderation access`, precedence `0`, rồi chọn **Create group**.
4. Chọn **Create group** lần nữa.
5. Nhập group name `user`, description `Regular users`, precedence `10`, rồi chọn **Create group**.

Kết quả cần có:

| Group | Description | Precedence |
|---|---|---|
| `admin` | Administrators with moderation access | 0 |
| `user` | Regular users | 10 |

![Hai Cognito group của dự án](/images/5-Workshop/5.4-Cognito-Auth/cognito_groups_setup.png)

## 4. Tạo người dùng thử và gán group

1. Có thể đăng ký từ frontend ở phần Testing, hoặc mở tab **Users** → **Create user** để tạo thủ công.
2. Nếu tạo từ Console, nhập email hợp lệ và chọn gửi invitation hoặc đánh dấu email verified theo mục đích lab.
3. Mở người dùng sau khi tài khoản ở trạng thái phù hợp, chọn **Add user to group**.
4. Chọn `user` cho tài khoản thường hoặc `admin` cho tài khoản kiểm thử chức năng moderation.
5. Đăng xuất rồi đăng nhập lại trên frontend sau khi đổi group để nhận ID token mới chứa claim `cognito:groups`.

Dự án hiện chưa có Post Confirmation trigger tự động thêm người đăng ký vào group `user`; đăng ký thành công không đồng nghĩa tài khoản đã thuộc group.

## 5. Ghi lại thông số tích hợp

1. Trong **User pool overview**, sao chép **User Pool ID**.
2. Trong **App integration** → **App clients**, mở app client và sao chép **Client ID**.
3. Xác nhận app client không có client secret và SRP authentication được bật.
4. Dùng User Pool ID cho API Gateway authorizer; dùng cả User Pool ID và Client ID cho biến môi trường của Amplify.
