---
title: "Kiểm thử và xác minh"
date: 2024-01-01
weight: 9
chapter: false
pre: " <b> 5.9. </b> "
---

# Kiểm thử Smart Image Platform

Các bước dưới đây dùng deployment CDK `staging`. Nếu tài nguyên được tạo thủ công, thay tên theo tài nguyên tương ứng.

## 1. Xác thực người dùng

1. Mở Amplify branch URL hoặc custom domain trỏ đến Amplify.
2. Đăng ký bằng email, tên và mật khẩu đáp ứng password policy.
3. Kiểm tra email từ Cognito, nhập mã xác nhận và hoàn tất đăng ký.
4. Nếu kiểm thử quyền group, mở Cognito Console → User Pool → **Users**, chọn tài khoản và thêm vào `user` hoặc `admin`.
5. Đăng nhập lại sau khi gán group để token mới chứa claim `cognito:groups`.
6. Trong DevTools → Network, kiểm tra request protected có header `Authorization`; không sao chép token vào ảnh chụp workshop.

![Màn hình sau khi xác nhận đăng ký](/images/5-Workshop/5.7-Testing-Validation/react_app_login.png)

## 2. Upload và xử lý ảnh

1. Chọn JPEG hoặc PNG hợp lệ trên giao diện Upload Images.
2. Chọn **Upload** và kiểm tra request lấy presigned URL trả về thành công.
3. Xác nhận trình duyệt PUT trực tiếp object vào raw bucket; API Gateway/Lambda không nhận toàn bộ binary của ảnh.
4. Mở raw bucket và kiểm tra object nằm dưới `users/<user-id>/`.
5. Theo dõi trạng thái ảnh trong giao diện; quá trình xử lý là bất đồng bộ nên frontend cần polling cho đến trạng thái cuối.
6. Mở processed bucket và kiểm tra các prefix `resized/` và `thumbnails/` dưới `users/<user-id>/`.
7. So sánh kích thước object gốc, resized và thumbnail để xác nhận Sharp đã tạo output, không chỉ sao chép file.

![Các thư mục output trong processed bucket staging](/images/5-Workshop/5.7-Testing-Validation/s3_processed_objects.png)

## 3. Metadata và Rekognition

1. Mở DynamoDB Console → **Tables** → `SmartImage-Images-staging`.
2. Chọn **Explore table items** và tìm item theo user/image vừa upload.
3. Mở item ở chế độ xem và kiểm tra:

- `PK`/`SK` đúng access pattern.
- `status` phản ánh tiến trình xử lý.
- `thumbnailKey`/`resizedKey` trỏ đến processed bucket.
- `aiTags`, moderation labels/status và EXIF được thêm khi các bước tương ứng hoàn tất.

![Kiểm tra aiTags trên item DynamoDB](/images/5-Workshop/5.7-Testing-Validation/dynamodb_item_tags.png)

Ảnh chỉ dùng để quan sát; không chỉnh sửa `aiTags` thủ công trên màn hình Edit item.

4. Nếu metadata xử lý ảnh có nhưng chưa có nhãn AI, kiểm tra DynamoDB stream, event source mapping của AiAnalyzer và log trước khi kết luận Rekognition lỗi.

## 4. Frontend gallery

1. Quay lại frontend và mở **My Gallery**.
2. Refresh hoặc chờ lần polling kế tiếp, sau đó chọn ảnh đã xử lý.
3. Kiểm tra preview, trạng thái, metadata và nhãn AI.
4. Thử tải/xem ảnh; URL trả về phải là presigned GET URL có thời hạn, không phải public S3 URL cố định.
5. Mở **Community Gallery** và kiểm tra request `GET /v1/images/public` không yêu cầu token.
6. Đăng xuất rồi mở My Gallery; route được bảo vệ phải yêu cầu đăng nhập.

![Ảnh ở trạng thái COMPLETED và các nhãn Rekognition](/images/5-Workshop/5.7-Testing-Validation/react_app_dashboard.png)

## 5. Logs, metrics và cảnh báo

1. Mở CloudWatch Console → **Logs** → **Log groups**.
2. Mở log stream mới nhất của từng Lambda, đối chiếu timestamp với lần upload vừa thực hiện và tìm request/image ID tương ứng.
3. Kiểm tra API access log để xác nhận method, path và status code.
4. Mở **Dashboards** → `SmartImage-staging-Operations` và đặt time range bao phủ lần test.
5. Kiểm tra:

- Lambda log groups có tên tương ứng `SmartImage-ApiHandler-staging`, `SmartImage-ImageProcessor-staging`, `SmartImage-AiAnalyzer-staging`.
- API access log group `/aws/apigateway/SmartImage-staging`.
- Dashboard `SmartImage-staging-Operations`.
- SNS topic `SmartImage-Alarms-staging`; email subscription phải được xác nhận trước khi nhận cảnh báo.
- SQS queues `SmartImage-ImageProcessorDlq-staging` và `SmartImage-AiAnalyzerDlq-staging`.

Alarm Lambda Errors dùng `Sum` trong 5 phút, threshold 5 và toán tử **GreaterThanThreshold**; cần ít nhất 6 lỗi trong một chu kỳ để chuyển sang ALARM.

Không dùng một invocation thành công để kết luận alarm hoặc DLQ hoạt động; hai cơ chế này cần lỗi không được handler nuốt và cần đủ số lần vượt threshold.

## 6. Kiểm thử lỗi

### A. Frontend validation

1. Chọn file có extension không được hỗ trợ như `.txt`.
2. Xác nhận frontend từ chối file trước khi upload.
3. Kiểm tra raw bucket không có object mới. Trường hợp này không tạo Lambda error và không dùng để thử alarm.

### B. Backend validation

1. Chỉ trong `staging`, tải trực tiếp một object có extension ảnh nhưng nội dung hỏng vào prefix `users/<test-user>/` của raw bucket.
2. Mở log ImageProcessor và xác nhận validation/Sharp ghi nhận lỗi.
3. Kiểm tra item thử nghiệm không bị hiển thị như ảnh `COMPLETED` hợp lệ.

### C. Giới hạn hiện tại của phép thử

`ImageProcessor` và `AiAnalyzer` hiện bắt lỗi mà không ném lại trong một số nhánh. Khi invocation vẫn được Lambda xem là thành công, metric `Errors`, retry và DLQ sẽ không hoạt động như một lỗi chưa xử lý. Chỉ ghi nhận SNS/DLQ là đã kiểm thử thành công sau khi error handling được sửa để trả lỗi, hoặc khi dùng một bài test alarm độc lập có kiểm soát.

## 7. Ghi nhận kết quả

Ghi lại timestamp, image ID, status API, key của raw/processed object, trạng thái DynamoDB và log stream tương ứng. Với mỗi bước, phân biệt rõ **đã quan sát được** và **kết quả mong đợi**; không ghi alarm/DLQ là thành công nếu chưa có bằng chứng trên Console.

> Không tạo lỗi lặp lại trên production. Xóa object/item thử nghiệm sau khi kiểm tra.
