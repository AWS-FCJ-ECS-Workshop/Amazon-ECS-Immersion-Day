+++
title = "Kết luận"
date = 2024-08-19T00:38:32+07:00
weight = 7
chapter = false
pre = "<b>7. </b>"
+++

Trong mô-đun Tự động hóa này, chúng ta đã triển khai thành công một số thành phần quan trọng giúp tự động hóa việc triển khai các ứng dụng dạng container lên Amazon ECS bằng cách sử dụng AWS CodePipeline, bao gồm:

1. **Tích hợp AWS CodePipeline với ECS:**
   Chúng ta đã tự động hóa toàn bộ quy trình từ khi có thay đổi trong mã nguồn cho đến khi triển khai, giúp tăng tốc độ và độ tin cậy của việc phát hành phần mềm.

2. **Bốn giai đoạn chính trong Pipeline:**

   1. Giai đoạn Source (Nguồn): Lấy mã nguồn mới nhất từ kho mã như GitHub hoặc AWS CodeCommit.

   2. Giai đoạn Build (Xây dựng): Biên dịch mã nguồn, tạo ảnh Docker bằng AWS CodeBuild và tùy chọn chạy các bài kiểm tra.

   3. Đẩy lên Registry: Đẩy ảnh Docker đã xây dựng lên Amazon ECR, có thể kèm theo quét bảo mật tùy chọn.

   4. Giai đoạn Deploy (Triển khai): Cập nhật dịch vụ ECS bằng ảnh Docker mới bằng cách cập nhật task definition và khởi chạy các task mới.

3. **Chiến lược Triển khai Cuốn Chiếu (Rolling Deployment):**

   - Chúng ta đã triển khai chiến lược cập nhật cuốn chiếu để dần thay thế phiên bản cũ bằng phiên bản mới, giảm thiểu thời gian gián đoạn và đảm bảo dịch vụ luôn khả dụng.

   - Chiến lược này được kiểm soát thông qua các tham số chính như minimumHealthyPercent và maximumPercent để quản lý tốc độ và độ ổn định của việc triển khai.

4. **Các bước Kiểm thử & Phê duyệt (Tùy chọn):**

   - Thêm các bài kiểm thử tích hợp hoặc kiểm thử khói sau khi triển khai.

   - Bao gồm các bước phê duyệt thủ công để tăng cường kiểm soát trước khi tiếp tục triển khai.

5. **Giám sát & Khôi phục (Rollback):**

   - Kích hoạt giám sát thông qua CloudWatch Logs, sự kiện dịch vụ ECS và cảnh báo từ CloudWatch.

   - Cấu hình tự động quay lại phiên bản ổn định trước đó nếu quá trình triển khai gặp lỗi.

![Conclusion](/images/7/ECS-Pipeline-7-1.png?width=90pc)

#### Các bước tiếp theo

Tiếp tục hành trình học tập Amazon ECS của bạn bằng cách khám phá các mô-đun workshop chuyên sâu sau:

1. [Storage](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/storage) – Tích hợp Amazon EFS để lưu trữ dữ liệu lâu dài

Mỗi mô-đun đều cung cấp trải nghiệm thực hành chi tiết giúp bạn xây dựng các ứng dụng ECS sẵn sàng cho môi trường sản xuất.

#### Người đóng góp

Le Minh Nghia - https://www.linkedin.com/in/minhnghia2k3/

Tran Doan Cong Ly - https://www.linkedin.com/in/trandoancongly/

#### Tài liệu tham khảo

Automation - https://catalog.workshops.aws/ecs-immersion-day/en-US/80-ci-cd

Rolling Deployent - https://catalog.workshops.aws/ecs-immersion-day/en-US/80-ci-cd/rolling
