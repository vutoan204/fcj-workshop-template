---
title: "Giới thiệu sản phẩm"
date: 2026-07-21
weight: 8
chapter: false
pre: " <b> 5.8. </b> "
---

# Smart Image Platform trên môi trường thực tế

Phần này giới thiệu phiên bản Smart Image Platform đã được triển khai và vận hành trên domain thực. Các ảnh minh họa được trích từ video demo của hệ thống, không phải dữ liệu mô phỏng trên AWS Console.

> Deployment production có thể sử dụng tên branch, stage và tài nguyên khác với môi trường `staging` trong các phần hướng dẫn trước. Kiến trúc ứng dụng và luồng xử lý chính vẫn tương ứng với dự án CDK.

## Thông tin truy cập

| Nội dung | Liên kết |
|---|---|
| Ứng dụng | [https://www.minphstudio.com/](https://www.minphstudio.com/) |
| Mã nguồn | [hngluc/AWS-Project](https://github.com/hngluc/AWS-Project) |
| Video demo | [Thư mục video trên Google Drive](https://drive.google.com/drive/folders/1c4NO1Vn2drC_M_2BfU2TzMpQmCSlOaOd?hl=vi) |

Video đầy đủ dài khoảng 2 phút 49 giây, trình bày luồng đăng ký, xác thực email, upload ảnh, xử lý AI, chỉnh sửa, tìm kiếm và chia sẻ ảnh.

## 1. Đăng nhập và truy cập Community Gallery

Ứng dụng sử dụng Amazon Cognito cho đăng ký, xác nhận email và đăng nhập. Người dùng chưa đăng nhập vẫn có thể chọn **Xem ảnh cộng đồng** để truy cập các ảnh đã được công khai; My Gallery, upload và các thao tác quản lý yêu cầu phiên đăng nhập hợp lệ.

![Màn hình đăng nhập trên domain minphstudio.com](/images/5-Workshop/5.8-Product-Showcase/live_sign_in.png)

## 2. Upload trực tiếp và xử lý bất đồng bộ

Màn hình Upload hỗ trợ kéo thả hoặc chọn JPEG, PNG, WEBP và GIF, tối đa 25 MB. Sau khi API tạo presigned URL, trình duyệt tải file trực tiếp lên raw S3 bucket.

Khu vực **Processing Queue** hiển thị trạng thái của từng file. Trong ảnh minh họa, file đã được upload và đang ở bước **AI Processing**. Badge **Connected to AWS** xác nhận frontend đang sử dụng backend AWS thay vì demo mode.

![Ảnh đang được xử lý trong Processing Queue](/images/5-Workshop/5.8-Product-Showcase/upload_processing.png)

Luồng phía sau giao diện gồm:

1. API Handler tạo metadata và presigned PUT URL.
2. Trình duyệt upload ảnh gốc trực tiếp vào Amazon S3.
3. S3 Event kích hoạt Image Processor để tạo resized image, thumbnail và trích xuất EXIF.
4. DynamoDB Stream kích hoạt AiAnalyzer.
5. Amazon Rekognition tạo object labels và moderation labels.
6. Metadata cuối cùng được cập nhật để frontend hiển thị trạng thái `COMPLETED`.

## 3. Image Insights và nhãn AI

Sau khi pipeline hoàn tất, cửa sổ **Image Insights & Controls** tổng hợp trạng thái xử lý, visibility, thời điểm upload, dung lượng, EXIF và các đối tượng được nhận diện. Mỗi nhãn AI đi kèm confidence score, giúp phân biệt kết quả nhận diện mạnh và yếu.

![EXIF và các nhãn AI do Rekognition tạo](/images/5-Workshop/5.8-Product-Showcase/ai_image_insights.png)

Tại đây có thể:

- Chuyển ảnh giữa `PRIVATE` và `PUBLIC`.
- Tải ảnh gốc bằng presigned download URL có thời hạn.
- Mở trình chỉnh sửa hoặc WebGL Shader Lab.
- Xóa ảnh cùng các object liên quan khi không còn sử dụng.

## 4. Chỉnh sửa ảnh và lưu thành bản sao

Trình chỉnh sửa cơ bản cung cấp brightness, contrast, saturation, xoay ±90° và lật ngang/dọc. Nút **Save as Copy** tạo một ảnh mới thay vì ghi đè ảnh gốc, nhờ đó bản ban đầu vẫn được giữ trong gallery.

![Chỉnh brightness, contrast, saturation và transform](/images/5-Workshop/5.8-Product-Showcase/image_editor.png)

## 5. WebGL Shader Lab

WebGL Shader Lab cho phép xếp chồng tối đa năm hiệu ứng GPU. Danh sách gồm các hiệu ứng như grayscale, color tint, pixelate, chromatic aberration, wave distortion, VHS/glitch, halftone, kaleidoscope và water ripple. Thứ tự và tham số effect có thể thay đổi trước khi lưu kết quả thành bản sao.

![Xếp chồng hiệu ứng trong WebGL Shader Lab](/images/5-Workshop/5.8-Product-Showcase/webgl_shader_lab.png)

Phần chỉnh sửa chạy ở frontend. Chỉ khi chọn **Save as Copy**, kết quả mới được tạo thành file mới và đi qua luồng lưu trữ của ứng dụng.

## 6. Quản lý ảnh trong My Gallery

My Gallery hiển thị ảnh thuộc tài khoản đang đăng nhập cùng trạng thái xử lý và visibility. Có thể mở chi tiết, chọn nhiều ảnh để xóa hàng loạt, refresh trạng thái hoặc tiếp tục chỉnh sửa một ảnh đã xử lý.

![Các ảnh của người dùng trong My Gallery](/images/5-Workshop/5.8-Product-Showcase/my_gallery.png)

Thumbnail và preview được đọc bằng URL có thời hạn; raw và processed buckets không cần public-read policy.

## 7. Tìm kiếm bằng nhãn AI

AI Tag Search sử dụng các nhãn được Amazon Rekognition ghi vào metadata. Ví dụ trong video, từ khóa `city` trả về các ảnh có nhãn tương ứng và không yêu cầu gắn tag thủ công ngay khi upload.

![Kết quả AI Tag Search cho từ khóa city](/images/5-Workshop/5.8-Product-Showcase/ai_tag_search.png)

Kết quả tìm kiếm vẫn tuân theo visibility và quyền truy cập, tránh hiển thị ảnh private của tài khoản khác.

## 8. Community Gallery

Community Gallery tập hợp các ảnh `PUBLIC`, đã xử lý hoàn tất và vượt qua điều kiện moderation. Gallery có thể được xem khi chưa đăng nhập, trong khi các thao tác thay đổi visibility hoặc xóa ảnh vẫn yêu cầu chủ sở hữu đăng nhập.

![Community Gallery trên deployment thực tế](/images/5-Workshop/5.8-Product-Showcase/community_gallery.png)

## Kết quả trình diễn

Deployment thực tế xác minh được chuỗi chức năng từ frontend đến AWS backend:

- Cognito registration, email verification và authenticated session.
- Upload trực tiếp lên S3 bằng presigned URL.
- Xử lý thumbnail/resized image và EXIF bằng Lambda/Sharp.
- Phân tích nhãn và moderation bằng Amazon Rekognition.
- My Gallery, Community Gallery, AI Tag Search và visibility controls.
- Basic editor, WebGL Shader Lab, download và bulk delete.

Phần [Kiểm thử và xác minh](../5.9-Testing-Validation/) tiếp theo trình bày cách đối chiếu các thao tác trên với S3, DynamoDB, Lambda Logs, CloudWatch metrics và alarms.
