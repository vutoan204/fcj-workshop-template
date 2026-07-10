---
title: "Cấu hình Storage & Cơ sở dữ liệu"
date: 2024-01-01
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---


# Cấu hình Storage & Cơ sở dữ liệu (AWS Console)

Trong phần này, bạn sẽ tạo các lớp lưu trữ (Amazon S3 buckets) và cơ sở dữ liệu (Amazon DynamoDB) cần thiết cho Smart Image Platform.

---

### Bước 1: Tạo các Amazon S3 Buckets

Nền tảng sử dụng hai S3 bucket riêng biệt nhằm đảm bảo tính bảo mật và ngăn ngừa rủi ro xảy ra vòng lặp vô tận khi thực thi Lambda:
1. **Raw Bucket:** Lưu trữ hình ảnh gốc do người dùng upload lên.
2. **Processed Bucket:** Lưu trữ thumbnail và ảnh đã qua xử lý tối ưu dung lượng.

#### A. Tạo Raw Images Bucket
1. Mở [Amazon S3 Console](https://s3.console.aws.amazon.com/).
2. Chọn **Create bucket**.
3. **Bucket name:** Điền tên duy nhất, ví dụ: `smartimage-raw-bucket-<ten-cua-ban>`.
4. **AWS Region:** Chọn `ap-southeast-1` (Singapore).
5. **Object Ownership:** Giữ mặc định là **ACLs disabled (recommended)**.
6. **Block Public Access settings for this bucket:** Tích chọn **Block *all* public access** (Rất quan trọng).
7. **Bucket Key:** Giữ mặc định chọn **Enable** dưới mục Encryption settings.
8. Chọn **Create bucket**.

![Cấu hình S3 Raw Bucket](/images/5-Workshop/5.3-Storage-Database/s3_raw_setup.png)

#### B. Tạo Processed Images Bucket
1. Chọn lại **Create bucket**.
2. **Bucket name:** Điền tên duy nhất, ví dụ: `smartimage-processed-bucket-<ten-cua-ban>`.
3. **AWS Region:** Chọn `ap-southeast-1` (Singapore).
4. **Block Public Access settings for this bucket:** Tích chọn **Block *all* public access**.
5. Chọn **Create bucket**.

![Cấu hình S3 Processed Bucket](/images/5-Workshop/5.3-Storage-Database/s3_processed_setup.png)

---

### Bước 2: Tạo Amazon DynamoDB Tables

Chúng ta sẽ khởi tạo ba bảng DynamoDB: `SmartImage-Images` (bảng lưu trữ metadata chính), `SmartImage-UserQuotas` (bảng quản lý hạn mức người dùng), và `SmartImage-UserProfiles` (bảng thông tin hồ sơ).

#### A. Tạo bảng Images chính
1. Mở [Amazon DynamoDB Console](https://console.aws.amazon.com/dynamodb/).
2. Chọn **Create table**.
3. **Table name:** Điền `SmartImage-Images`.
4. **Partition key:** Điền `PK` (Kiểu dữ liệu: **String**).
5. **Sort key:** Tích chọn **Add sort key** và điền `SK` (Kiểu dữ liệu: **String**).
6. **Table settings:** Chọn **Customize settings**.
7. **Read/write capacity settings:** Chọn **On-demand** (Để hệ thống tự co dãn và tiết kiệm chi phí trong Free Tier).
8. Giữ các cấu hình khác mặc định và chọn **Create table**.

![Cấu hình DynamoDB Table](/images/5-Workshop/5.3-Storage-Database/dynamodb_table_setup.png)

#### B. Kích hoạt DynamoDB Streams
*Lưu ý: Trên giao diện AWS Console mới, luồng DynamoDB streams được cấu hình kích hoạt sau khi bảng đã được tạo.*
1. Trong danh sách bảng DynamoDB, bấm chọn bảng `SmartImage-Images` vừa tạo.
2. Chuyển sang tab **Exports and streams**.
3. Cuộn xuống mục **DynamoDB stream details** và chọn **Turn on** (hoặc **Enable**).
4. **View type:** Chọn **New and old images** (Bắt buộc để kích hoạt AI Analyzer Lambda sau này).
5. Chọn **Turn on stream**.

#### C. Cấu hình các Global Secondary Indexes (GSIs)
Cấu hình các chỉ mục phụ tại trang chi tiết bảng:
1. Bên dưới bảng `SmartImage-Images`, chọn tab **Indexes**.
2. Chọn **Create index** (hoặc **Create global index**).
3. **Partition key:** Điền `GSI1PK` (String).
4. **Sort key:** Tích chọn **Add sort key** và điền `GSI1SK` (String).
5. **Index name:** Điền `GSI1-TagIndex-v2`.
6. **Attribute projections:**
   * **Cách 1: Khuyên dùng (Giống hệt cấu hình CDK):** Tích chọn **Include**. Do bảng mới tạo chưa có dữ liệu, bạn cần tự gõ thủ công từng tên thuộc tính dưới đây và bấm **Add** sau mỗi từ:
     * `imageId`, `userId`, `thumbnailKey`, `originalFilename`, `createdAt`, `moderationStatus`, `imagePK`, `imageSK`
   * **Cách 2: Đơn giản hơn:** Tích chọn **All** (Chiếu tất cả các thuộc tính của bảng chính sang GSI. Lựa chọn này cấu hình nhanh hơn trên Console và tránh bị thiếu/gõ sai thuộc tính, nhưng sẽ tốn thêm một chút dung lượng lưu trữ).
7. Chọn **Create index**.
8. Thực hiện các bước tương tự để tạo GSI thứ hai:
   * **Partition key:** `GSI2PK` (String).
   * **Sort key:** `GSI2SK` (String).
   * **Index name:** `GSI2-ModerationIndex`.
   * **Attribute projections:** Chọn **Include** và thêm: `imageId`, `userId`, `originalKey`, `moderationLabels`, `thumbnailKey`, `createdAt` (hoặc chọn **All** cho đơn giản).
9. Chọn **Create index**.

![Cấu hình DynamoDB GSIs](/images/5-Workshop/5.3-Storage-Database/dynamodb_gsi_setup.png)

#### D. Tạo bảng Quota người dùng
1. Chọn mục **Tables** -> **Create table**.
2. **Table name:** Điền `SmartImage-UserQuotas`.
3. **Partition key:** Điền `PK` (Kiểu dữ liệu: **String**).
4. **Sort key:** Tích chọn **Add sort key** và điền `SK` (Kiểu dữ liệu: **String**).
5. **Table settings:** Chọn **Customize settings** và đặt **Read/write capacity settings** là **On-demand**.
6. Chọn **Create table**.

#### E. Tạo bảng Profile người dùng
1. Chọn mục **Tables** -> **Create table**.
2. **Table name:** Điền `SmartImage-UserProfiles`.
3. **Partition key:** Điền `PK` (Kiểu dữ liệu: **String**).
4. **Sort key:** Tích chọn **Add sort key** và điền `SK` (Kiểu dữ liệu: **String**).
5. **Table settings:** Chọn **Customize settings** và đặt **Read/write capacity settings** là **On-demand**.
6. Chọn **Create table**.
