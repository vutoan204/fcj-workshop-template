---
title: "Console - Storage và Database"
date: 2024-01-01
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

# Storage và Database trên AWS Console (tùy chọn)

> Phần này minh họa cấu hình tương đương trên Console. Bỏ qua toàn bộ bước tạo tài nguyên nếu các stack CDK `staging` đã được deploy.

## 1. Amazon S3

Hệ thống sử dụng hai bucket private để tách ảnh gốc và kết quả xử lý. Khi tạo thủ công, dùng tên duy nhất có hậu tố `staging`; tên vật lý không cần giống chính xác tên do CDK tạo.

### Raw bucket

1. Mở [Amazon S3 Console](https://s3.console.aws.amazon.com/) và kiểm tra Region đang là **Asia Pacific (Singapore) `ap-southeast-1`**.
2. Chọn **Create bucket**.
3. Ở **Bucket type**, chọn **General purpose**.
4. Nhập tên duy nhất, ví dụ `smartimage-staging-raw-images-<account-id>`. Tên này chỉ dùng cho nhánh Console và không cần trùng tên vật lý do CDK sinh ra.
5. Ở **Object Ownership**, giữ **ACLs disabled (recommended)**.
6. Ở **Block Public Access settings**, giữ **Block all public access** và toàn bộ bốn tùy chọn con ở trạng thái bật.
7. Ở **Bucket Versioning**, chọn **Enable**.
8. Trong **Default encryption**, chọn **Server-side encryption with Amazon S3 managed keys (SSE-S3)**. Giữ **Bucket Key** ở **Disable**; tùy chọn này chỉ có ý nghĩa với SSE-KMS.
9. Chọn **Create bucket**.

![Thiết lập ban đầu cho raw bucket staging](/images/5-Workshop/5.3-Storage-Database/s3_raw_setup.png)

#### Cấu hình sau khi tạo raw bucket

1. Mở bucket vừa tạo, chọn **Permissions** → **Cross-origin resource sharing (CORS)** → **Edit**.
2. Thêm rule cho phép frontend gọi `PUT` và `POST`, cho phép mọi header cần thiết, expose `ETag` và đặt max age `3600`. Trong lab có thể dùng origin của Amplify; chỉ dùng `*` tạm thời khi chưa có URL frontend và siết lại sau khi deploy.
3. Chọn **Management** → **Create lifecycle rule**.
4. Áp dụng rule cho toàn bộ object trong bucket.
5. Cấu hình chuyển object hiện tại sang **Standard-IA** sau 90 ngày và **Glacier Flexible Retrieval** sau 365 ngày.
6. Tạo thêm rule hủy multipart upload chưa hoàn thành sau **7 ngày** và rule xóa noncurrent versions sau **30 ngày**. Kiểm tra kỹ hai hành động xóa trước khi áp dụng với dữ liệu thật.

Ví dụ CORS tối thiểu để minh họa:

```json
[
  {
    "AllowedHeaders": ["*"],
    "AllowedMethods": ["PUT", "POST"],
    "AllowedOrigins": ["https://<amplify-branch-domain>"],
    "ExposeHeaders": ["ETag"]
  }
]
```

### Processed bucket

1. Quay lại danh sách bucket và chọn **Create bucket**.
2. Nhập tên duy nhất, ví dụ `smartimage-staging-processed-images-<account-id>`, cùng Region `ap-southeast-1`.
3. Giữ **ACLs disabled** và **Block all public access**.
4. Ở **Bucket Versioning**, chọn **Disable** để khớp CDK hiện tại.
5. Chọn **SSE-S3** và giữ **Bucket Key** ở **Disable**.
6. Chọn **Create bucket**.
7. Trong tab **Permissions**, thêm CORS cho `GET` và `HEAD` từ origin frontend.
8. Trong tab **Management**, tạo lifecycle rule và chọn **Delete expired object delete markers or incomplete multipart uploads**; đặt hủy multipart upload chưa hoàn thành sau **7 ngày**. CORS của bucket này cho phép `GET`/`HEAD`, mọi header cần thiết, expose `ETag` và max age `3600`.

![Versioning và mã hóa của processed bucket](/images/5-Workshop/5.3-Storage-Database/s3_processed_setup.png)

> Cả hai bucket đều private. Frontend sử dụng presigned URL; không bật static website hosting hoặc public-read policy.

## 2. Amazon DynamoDB

Tạo ba bảng ở chế độ **On-demand**, bật mã hóa mặc định và Point-in-time recovery:

| Bảng minh họa | Partition key | Sort key | Chức năng |
|---|---|---|---|
| `SmartImage-Images-staging` | `PK` (String) | `SK` (String) | Metadata ảnh và access pattern liên quan |
| `SmartImage-UserQuotas-staging` | `PK` (String) | `SK` (String) | Hạn mức tải lên/lưu trữ |
| `SmartImage-UserProfiles-staging` | `PK` (String) | `SK` (String) | Hồ sơ người dùng |

![Khai báo khóa chính cho bảng Images](/images/5-Workshop/5.3-Storage-Database/dynamodb_table_setup.png)

### Tạo bảng Images

1. Mở [Amazon DynamoDB Console](https://console.aws.amazon.com/dynamodb/) và chọn **Tables** → **Create table**.
2. Nhập table name `SmartImage-Images-staging`.
3. Nhập partition key `PK`, kiểu **String**.
4. Chọn **Add sort key**, nhập `SK`, kiểu **String**.
5. Chọn **Customize settings** và đặt capacity mode là **On-demand**.
6. Giữ mã hóa mặc định **Owned by Amazon DynamoDB**.
7. Trong **Point-in-time recovery**, chọn **Turn on** nếu tùy chọn này xuất hiện trong wizard; nếu không, bật sau khi bảng ở trạng thái **Active**.
8. Chọn **Create table** và chờ trạng thái chuyển sang **Active** trước khi tạo stream hoặc index.

### Stream của bảng Images

1. Mở bảng `Images` và chọn **Exports and streams**.
2. Bật DynamoDB Stream với view type **New and old images**.
3. Chưa tạo Lambda trigger tại đây; trigger được minh họa trong phần Backend.

### Tạo Global Secondary Indexes

1. Mở bảng `SmartImage-Images-staging` và chọn tab **Indexes**.
2. Chọn **Create index**.
3. Với GSI thứ nhất, nhập partition key `GSI1PK` và sort key `GSI1SK`, đều kiểu **String**.
4. Nhập index name `GSI1-TagIndex-v2`.
5. Ở **Attribute projections**, chọn **Include** rồi thêm lần lượt `imageId`, `userId`, `thumbnailKey`, `originalFilename`, `createdAt`, `moderationStatus`, `imagePK`, `imageSK`.
6. Chọn **Create index** và chờ index ở trạng thái **Active**.
7. Chọn **Create index** lần nữa; nhập `GSI2PK`, `GSI2SK`, tên `GSI2-ModerationIndex`.
8. Chọn **Include** và thêm `imageId`, `userId`, `originalKey`, `moderationLabels`, `thumbnailKey`, `createdAt`.
9. Chọn **Create index**.

Cấu hình cuối cùng phải tương ứng:

- `GSI1-TagIndex-v2`: `GSI1PK` và `GSI1SK` (String), projection `INCLUDE` gồm `imageId`, `userId`, `thumbnailKey`, `originalFilename`, `createdAt`, `moderationStatus`, `imagePK`, `imageSK`.
- `GSI2-ModerationIndex`: `GSI2PK` và `GSI2SK` (String), projection `INCLUDE` gồm `imageId`, `userId`, `originalKey`, `moderationLabels`, `thumbnailKey`, `createdAt`.

![Khai báo key schema cho GSI1](/images/5-Workshop/5.3-Storage-Database/dynamodb_gsi_setup.png)

> Ảnh chỉ minh họa key schema của GSI1. Có thể chọn projection `ALL` cho bài Console ngắn, nhưng đây là phương án tùy chọn và khác với CDK.

### Tạo hai bảng hỗ trợ

Thực hiện hai lần quy trình **Tables** → **Create table**:

1. Tạo `SmartImage-UserQuotas-staging` với `PK` và `SK`, đều kiểu String.
2. Tạo `SmartImage-UserProfiles-staging` với `PK` và `SK`, đều kiểu String.
3. Với cả hai bảng, chọn **On-demand**, mã hóa mặc định và bật point-in-time recovery.
4. Không tạo DynamoDB Stream hoặc GSI cho hai bảng này vì CDK hiện không khai báo các cấu hình đó.

### Kiểm tra kết quả

- Cả ba bảng ở trạng thái **Active**.
- Bảng Images có hai GSI ở trạng thái **Active** và stream view type **New and old images**.
- Hai bucket vẫn hiển thị **Block public access: On**.
- Raw bucket bật versioning; processed bucket không bật versioning.
- Không có bucket policy cấp quyền public. Quyền truy cập sẽ được cấp cho Lambda role ở phần Backend.
- Chỉ cho phép truy cập HTTPS. CDK dùng `enforceSSL`; nếu tạo thủ công, có thể thêm bucket policy từ chối request có `aws:SecureTransport=false`.
