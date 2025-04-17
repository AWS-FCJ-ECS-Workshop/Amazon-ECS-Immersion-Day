+++
title = "Tạo S3 Bucket"
date = 2020-05-14T00:38:32+07:00
weight = 2
chapter = false
pre = "<b>2.2 </b>"
+++

### Amazon S3 Bucket – Kho lưu trữ Artifact cho Pipeline

Là một phần của việc thiết lập quy trình CI/CD, chúng ta sẽ tạo một bucket Amazon S3 để đóng vai trò là kho lưu trữ artifact cho AWS CodePipeline. Bucket này sẽ tạm thời chứa các artifact build (chẳng hạn như mã đã biên dịch, tham chiếu Docker image hoặc các descriptor triển khai) được chuyển giao giữa các stage khác nhau của pipeline.

**Tại sao nó quan trọng?**

- CodePipeline sử dụng S3 để lưu trữ và truyền dữ liệu giữa các stage Source, Build và Deploy.

- Nó đảm bảo rằng mỗi stage đều có quyền truy cập vào các output cần thiết để thực thi đúng cách.

- S3 cung cấp độ bền, khả năng mở rộng và độ tin cậy, khiến nó trở nên lý tưởng cho việc lưu trữ artifact.

**Các điểm chính:**

- Bucket S3 nên ở cùng region với pipeline và dịch vụ ECS của bạn để đảm bảo hiệu suất và tuân thủ.

- Nên bật versioning để lưu giữ các phiên bản lịch sử của artifact.

- Bạn có thể thiết lập các lifecycle policy để tự động dọn dẹp các artifact cũ và tiết kiệm chi phí lưu trữ.

- Tùy chọn, bạn có thể sử dụng bucket policy hoặc mã hóa S3 để tăng cường bảo mật.

Bucket này là một thành phần cơ bản của pipeline và phải được cấu hình đúng cách trước khi xây dựng phần còn lại của quy trình CI/CD.

### Tạo S3 Bucket

1. Truy cập [S3 Dashboard](https://ap-southeast-1.console.aws.amazon.com/s3)
   ![S3-1](/images/2/2.2-1.png?width=90pc)

2. Chọn **Create Bucket**
3. Nhập **Bucket name**
   ![S3-2](/images/2/2.2-2.png?width=90pc)

4. Giữ nguyên các phần còn lại theo mặc định, Chọn **Create Bucket**
   ![S3-3](/images/2/2.2-3.png?width=90pc)

5. Chúc mừng bạn, bạn đã tạo thành công một S3 Bucket.
   ![S3-4](/images/2/2.2-4.png?width=90pc)
