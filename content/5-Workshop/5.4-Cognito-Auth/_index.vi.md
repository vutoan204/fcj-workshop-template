---
title: "Xác thực người dùng với Amazon Cognito"
date: 2024-01-01
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---


# Cấu hình Xác thực Amazon Cognito

Trong phần này, bạn sẽ tạo và cấu hình một Amazon Cognito User Pool nhằm phục vụ cho hoạt động đăng ký, đăng nhập tài khoản người dùng, đồng thời tích hợp bảo vệ API Gateway.

Tùy thuộc vào trạng thái tài khoản và AWS Region của bạn, giao diện điều khiển Console có thể hiển thị dưới dạng **Trình hướng dẫn 6 bước chuẩn (Standard Wizard)** hoặc **Trình hướng dẫn thiết lập nhanh rút gọn (Simplified Wizard)**. Hãy chọn luồng cấu hình tương ứng dưới đây.

---

### Bước 1: Tạo Cognito User Pool

#### Trường hợp A: Sử dụng Giao diện thiết lập nhanh rút gọn (Khuyên dùng)
Nếu bạn nhìn thấy giao diện **"Set up resources for your application"** giống như trong screenshot:
1. **Define your application:**
   * **Application type:** Tích chọn **Single-page application (SPA)**. (Lưu ý quan trọng: Việc chọn SPA sẽ tự động thiết lập app client này thành một *Public Client* và *Không tạo client secret*, rất cần thiết để gọi API trực tiếp từ mã nguồn React của phía Client).
   * **Name your application:** Điền tên ứng dụng, ví dụ: `SmartImage-WebClient`.
2. **Configure options:**
   * **Options for sign-in identifiers:** Tích chọn **Email** (đảm bảo bỏ tích ở Username và Phone number).
   * **Self-registration:** Đảm bảo tích chọn **Enable self-registration** (cho phép người dùng tự đăng ký).
   * **Required attributes for sign-up:** Nhấp vào ô dropdown và chọn cả hai thuộc tính **email** và **name**.
3. **Add a return URL - optional:**
   * Để trống (chúng ta sẽ dùng các biểu mẫu đăng nhập tự tùy biến ở React frontend chứ không sử dụng giao diện đăng nhập mặc định Hosted UI của Cognito).
4. Nhấn **Create user directory** (hệ thống sẽ tự động khởi tạo User Pool và cấu hình client SPA đi kèm).

#### Trường hợp B: Sử dụng Trình hướng dẫn cấu hình 6 bước chuẩn (Standard 6-Step Wizard)
Nếu giao diện hiển thị dưới dạng biểu mẫu cấu hình nhiều bước:
1. Tại mục **Configure sign-in experience**:
   * **Cognito user pool sign-in options:** Tích chọn duy nhất ô **Email**. Nhấn **Next**.
2. Tại mục **Configure security requirements**:
   * **Password policy:** Chọn **Cognito defaults**.
   * **Multi-factor authentication (MFA):** Chọn **No MFA**. Nhấn **Next**.
3. Tại mục **Configure sign-up experience**:
   * Giữ tích chọn **Self-service sign-up**.
   * **Required attributes:** Chọn thêm **name** và **email**. Nhấn **Next**.
4. Tại mục **Configure message delivery**:
   * **Email provider:** Chọn **Send email with Cognito**. Nhấn **Next**.
5. Tại mục **Integrate app**:
   * **User pool name:** Điền `SmartImage-UserPool`.
   * **Hosted UI:** Không tích chọn.
   * **App client:** Chọn **Public client**.
   * **App client name:** Điền `SmartImage-WebClient`.
   * **Client secret:** Chọn **Don't generate a client secret** (Lưu ý: Bắt buộc chọn cái này để code React chạy ở client-side an toàn).
   * Chọn **Next**.
6. Tại mục **Review and create**: Nhấn **Create user pool**.

![Cấu hình Cognito User Pool](/images/5-Workshop/5.4-Cognito-Auth/cognito_userpool_setup.png)

---

### Bước 2: Thêm Custom Attribute

Chúng ta cần thêm thuộc tính tùy biến `custom:role` để kiểm soát xem người dùng là user thường hay admin.
1. Trong giao diện Cognito Console, bấm chọn User Pool bạn vừa tạo ở trên.
2. Chuyển sang tab **Sign-up**.
3. Cuộn xuống mục **Custom attributes** và chọn **Add custom attribute** (hoặc **Create custom attribute**).
4. **Attribute name:** Điền `role`.
5. **Type:** Chọn **String**.
6. **Constraints:** Đặt độ dài tối thiểu (Min length) là `1` và tối đa (Max length) là `20`.
7. Chọn **Save changes** (hoặc **Create**).

---

### Bước 3: Tạo các nhóm người dùng (User Groups)

Chúng ta định nghĩa hai nhóm quản trị:
1. Trong User Pool của bạn, chuyển sang tab **User groups**.
2. Chọn **Create group**.
3. **Group name:** Điền `admin`.
4. **Description:** Điền `Administrators with moderation access`.
5. **Precedence:** Điền `0` (Mức ưu tiên cao nhất).
6. Chọn **Create group**.
7. Chọn **Create group** một lần nữa.
8. **Group name:** Điền `user`.
9. **Description:** Điền `Regular users`.
10. **Precedence:** Điền `10`.
11. Chọn **Create group**.

![Cấu hình Cognito Groups](/images/5-Workshop/5.4-Cognito-Auth/cognito_groups_setup.png)

*Lưu ý: Hãy ghi lại hai thông số **User Pool ID** và **App Client ID** hiển thị ở trang thông tin tổng quan của User Pool. Bạn sẽ cần chúng để cấu hình API Authorizer và React frontend sau này.*
