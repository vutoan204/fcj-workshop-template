---
title: "Blog 2"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

# GIẢI PHÁP XỬ LÝ HÌNH ẢNH DYNAMIC TRÊN AWS VỚI AMAZON SAGEMAKER VÀ AMAZON BEDROCK

Việc ứng dụng Generative AI để xây dựng các công cụ hỗ trợ sáng tạo (creative assisting tools) đòi hỏi chúng ta phải giải quyết đồng thời hai bài toán: lựa chọn mô hình học máy phù hợp và tối ưu hóa hạ tầng tính toán (inference infrastructure) để giảm thiểu chi phí vận hành.

Chuỗi bài viết này gồm hai phần trên trang blog của AWS đã trình bày chi tiết giải pháp xây dựng công cụ chỉnh sửa ảnh thông minh (Inpaint Eraser) kết hợp kỹ thuật Text-Guided Inpainting và Outpainting thông qua dịch vụ Amazon SageMaker và Amazon Bedrock.

---

### PHẦN 1: XÂY DỰNG AI INPAINT ERASER TRÊN AMAZON SAGEMAKER MME

Mục tiêu của phần này là tạo ra một công cụ xóa vật thể thông minh (Inpaint Eraser) hoạt động theo cơ chế hai bước: Tạo mặt nạ phân rã vật thể (Segmentation Mask) từ tọa độ điểm click chuột, sau đó thực hiện xóa và bù đắp chi tiết nền (Inpainting).

#### Các mô hình học máy (Machine Learning Models) được sử dụng:
* **Segment Anything Model (SAM) của Meta:** Đây là mô hình nền tảng (Foundation Model) được huấn luyện trên tập dữ liệu SA-1B (11 triệu hình ảnh và 1,1 tỷ mặt nạ). SAM cung cấp khả năng Zero-shot Generalization vượt trội, cho phép cô lập và tạo Segmentation Mask của bất kỳ vật thể nào dựa trên input là tọa độ điểm ảnh (pixel coordinate) mà không cần huấn luyện lại.
* **Resolution-robust Large Mask Inpainting với Fourier Convolutions (LaMa):** Khác với các mô hình inpainting truyền thống đi qua hai bước phức tạp, LaMa sử dụng kiến trúc đơn bước (single-step architecture) dựa trên các liên hợp Fourier nhanh (Fast Fourier Convolutions - FFCs). Thiết kế này giúp mô hình thu thập thông tin ngữ cảnh toàn cầu (global context) của toàn bộ bức ảnh ngay lập tức, mang lại kết quả xử lý tự nhiên và tốc độ inference nhanh hơn.

#### Kiến trúc hạ tầng trên AWS:
* **AWS SageMaker Multi-Model Endpoints (MME) chạy trên GPU (Amazon EC2 G5 instance):** Để tối ưu hóa chi phí phần cứng, hệ thống không chạy các GPU riêng biệt cho từng mô hình. Thay vào đó, SageMaker MME cho phép chia sẻ tài nguyên bộ nhớ GPU (time-sharing) giữa SAM và LaMa trong cùng một container phục vụ chung. Các model artifacts được lưu dưới dạng file nén (.tar.gz) trong Amazon S3 và sẽ được nạp động vào bộ nhớ GPU của Endpoint khi có request gửi tới.
* **NVIDIA Triton Inference Server với Python Backend:** Triton đóng vai trò là inference server chính. Các lập trình viên định nghĩa cấu trúc lớp TritonPythonModel (bao gồm các hàm initialize, execute để chạy xử lý mô hình) và tệp model configuration (config.pbtxt) hỗ trợ dynamic batching và dynamic tensor shapes (dims: [-1]) cho đầu vào dạng chuỗi mã hóa base64.
* **API Gateway và AWS Lambda:** Đóng vai trò là tầng API proxy nhận request từ Client, thực hiện chuyển tiếp payload đến SageMaker Endpoint và trả kết quả ảnh đã qua xử lý ngược lại Client.

![Kiến trúc Inpaint Eraser với SageMaker](/images/3-BlogsPosted/blog2-part1.png)

---

### PHẦN 2: TEXT-GUIDED INPAINTING VÀ OUTPAINTING VỚI AMAZON BEDROCK

Sau khi có nền tảng phân rã vật thể từ phần 1, phần 2 mở rộng giải pháp sang hướng chỉnh sửa ảnh có định hướng bằng văn bản (Language-guided editing) thông qua Amazon Bedrock - dịch vụ serverless được quản lý hoàn toàn để gọi API trực tiếp các mô hình Foundation Models lớn.

#### Kỹ thuật Language-Guided Inpainting (Thay thế vật thể theo văn bản):
* **Mô hình áp dụng:** Stable Diffusion XL (SDXL 1.0) thông qua API ID `stability.stable-diffusion-xl`.
* **Luồng hoạt động:** Payload gửi lên Bedrock bao gồm ảnh gốc (init_image) đã được mã hóa base64, ảnh mặt nạ (mask_image) được sinh ra từ mô hình SAM ở phần 1, và đoạn prompt mô tả chi tiết vật thể mới cần chèn (ví dụ: *"The Mona Lisa wearing a wig"*).
* **Các tham số điều khiển quan trọng:**
  * `cfg_scale`: Tham số kiểm soát mức độ bám sát của hình ảnh sinh ra đối với văn bản mô tả.
  * `style_preset`: Định hình trước phong cách nghệ thuật (như digital-art, photographic, cinematic).
  * `seed`: Số ngẫu nhiên dùng để khởi tạo quá trình sinh ảnh nhằm đảm bảo tính tái tạo (reproducibility).

#### Kỹ thuật Language-Guided Outpainting (Mở rộng viền ảnh theo văn bản):
* **Mô hình áp dụng:** Amazon Titan Image Generator (TIG v1) thông qua API ID `amazon.titan-image-generator-v1`.
* **Luồng hoạt động:**
  1. Tạo một khung canvas trống (canvas mở rộng) với kích thước mục tiêu mới (ví dụ từ kích thước gốc lên 1024x1024) và đặt ảnh gốc ở chính giữa.
  2. Tạo một mask ảnh tương ứng, trong đó vùng chứa ảnh gốc được tô đen hoàn toàn (để bảo vệ chi tiết gốc không bị mô hình ghi đè) và vùng mở rộng viền ngoài được tô trắng (chỉ định vùng cần vẽ thêm).
  3. Gửi payload đến Bedrock API với `taskType` được cấu hình cụ thể là `OUTPAINTING`.
* **Các tham số điều khiển quan trọng trong outPaintingParams:**
  * `text`: Prompt mô tả chi tiết về bối cảnh nền cần mở rộng.
  * `outPaintingMode`: Cấu hình `DEFAULT` để làm mềm và hòa trộn mượt mà đường biên giữa ảnh cũ và ảnh mới, hoặc `PRECISE` để giữ đường biên sắc nét.

![Xử lý Inpainting & Outpainting với Bedrock](/images/3-BlogsPosted/blog2-part2.png)

---

### KẾT LUẬN

Giải pháp lai (hybrid architecture) này thể hiện một hướng đi tối ưu cho doanh nghiệp:
1. **Amazon SageMaker MME tự quản lý:** Dành cho các mô hình mã nguồn mở kích thước vừa phải (như SAM và LaMa) để kiểm soát chi phí thực thi các tác vụ logic cơ bản, xử lý cục bộ với độ trễ thấp.
2. **Amazon Bedrock API:** Triệu gọi trực tiếp các mô hình tạo ảnh lớn, phức tạp (SDXL, Titan Image Generator) mà không phải chịu chi phí duy trì tài nguyên hạ tầng đắt đỏ lúc hệ thống không hoạt động.

### Facebook Post & Tài liệu tham khảo
* **Bài đăng gốc trên Facebook:** [AWS Study Group Post #2207155823382711](https://www.facebook.com/groups/awsstudygroupfcj/permalink/2207155823382711/)
* **Đường link tài liệu chính thức từ AWS:**
  * **Phần 1 (SageMaker, SAM, LaMa):** [Generative AI assists creative content with Inpaint Eraser and Amazon SageMaker](https://aws.amazon.com/blogs/media/generative-ai-assists-creative-content-with-inpaint-eraser-and-amazon-sagemaker/)
  * **Phần 2 (Bedrock, SDXL, Titan Image Generator):** [Generative AI assists creative workflows with text-guided inpainting and outpainting using Amazon Bedrock](https://aws.amazon.com/blogs/media/generative-ai-assists-creative-workflows-with-text-guided-inpainting-and-outpainting-using-amazon-bedrock/)