---
title: "Blog 1"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# TỐI ƯU HÓA HÌNH ẢNH HIỆU QUẢ VỚI AMAZON CLOUDFRONT VÀ AWS LAMBDA

Hình ảnh thường là yếu tố nặng nhất trên các trang web hiện đại. Việc tải ảnh dung lượng lớn không chỉ làm chậm tốc độ tải trang mà còn ảnh hưởng trực tiếp đến trải nghiệm người dùng và điểm số SEO, đặc biệt là chỉ số Largest Contentful Paint (LCP) của Google.

Để giải quyết vấn đề này, bài viết "Image Optimization using Amazon CloudFront and AWS Lambda" trên trang blog của AWS đã giới thiệu một giải pháp serverless hiệu quả để xử lý ảnh tự động và tối ưu hóa chi phí phân phối.

### Ý tưởng cốt lõi của giải pháp
Thay vì phải tạo thủ công và lưu trữ hàng loạt phiên bản ảnh với các kích thước khác nhau trong bộ nhớ S3 (gây lãng phí dung lượng), hệ thống sẽ chỉ lưu trữ một phiên bản ảnh gốc chất lượng cao. Việc chuyển đổi định dạng và thay đổi kích thước ảnh sẽ được thực hiện tự động ngay khi người dùng yêu cầu (on-the-fly) và được lưu vào bộ nhớ đệm (cache) để phục vụ cho các lượt truy cập sau.

### Chi tiết về luồng hoạt động của kiến trúc

1. **Yêu cầu từ người dùng:** Khi người dùng truy cập trang web, trình duyệt sẽ gửi yêu cầu tải ảnh với các tham số cụ thể về kích thước và định dạng phù hợp với màn hình thông qua Amazon CloudFront.
2. **Kiểm tra bộ nhớ đệm (Cache Check):** Amazon CloudFront sẽ kiểm tra xem phiên bản ảnh yêu cầu đã có sẵn trong bộ nhớ đệm hay chưa:
   * **Cache Hit (Có sẵn):** Ảnh được trả về ngay lập tức cho người dùng với tốc độ tối đa.
   * **Cache Miss (Chưa có sẵn):** Yêu cầu sẽ được chuyển tiếp đến một hàm AWS Lambda.
3. **Gọi hàm Lambda:** Hàm Lambda này sẽ lấy ảnh gốc từ kho lưu trữ Amazon S3.
4. **Xử lý on-the-fly:** Hàm thực hiện các thao tác xử lý như cắt, nén, thay đổi kích thước và chuyển đổi sang các định dạng tối ưu hơn như WebP hoặc AVIF.
5. **Trả về kết quả và lưu Cache:** Ảnh sau khi xử lý được trả về cho CloudFront. CloudFront vừa hiển thị cho người dùng vừa lưu lại vào bộ nhớ đệm để các yêu cầu tiếp theo không cần phải xử lý lại từ đầu.

### Ưu điểm nổi bật của kiến trúc
* **Nâng cao hiệu năng trang web:** Giảm đáng kể dung lượng tải trang, giúp trang web hiển thị nhanh hơn và cải thiện điểm số Core Web Vitals của Google.
* **Tiết kiệm chi phí lưu trữ và băng thông:** Doanh nghiệp không phải trả tiền để lưu trữ quá nhiều phiên bản ảnh tĩnh khác nhau trên S3. Đồng thời, dung lượng ảnh nhẹ hơn giúp giảm chi phí truyền tải dữ liệu (data transfer) của CloudFront.
* **Dễ dàng tích hợp:** Giải pháp này hoạt động cực kỳ hiệu quả khi kết hợp với các framework phát triển web hiện đại, tiêu biểu là tính năng tối ưu ảnh tự động của Next.js.

### Sơ đồ kiến trúc
![Sơ đồ tối ưu hóa hình ảnh](/images/3-BlogsPosted/blog1.png)

### Facebook Post & Tài liệu tham khảo
* **Bài đăng gốc trên Facebook:** [AWS Study Group Post #2206231570141803](https://www.facebook.com/groups/awsstudygroupfcj/permalink/2206231570141803/)
* **Bài viết chi tiết của AWS:** [Image Optimization using Amazon CloudFront and AWS Lambda](https://aws.amazon.com/blogs/networking-and-content-delivery/image-optimization-using-amazon-cloudfront-and-aws-lambda/)