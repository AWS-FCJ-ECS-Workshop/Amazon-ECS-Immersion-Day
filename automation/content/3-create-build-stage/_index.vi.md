+++
title = "Giai đoạn build được triển khai bằng AWS CodeBuild."
date = 2020-05-14T00:38:32+07:00
weight = 3
chapter = false
pre = "<b>3. </b>"
+++

### Xem xét Giai đoạn Build của CI/CD Pipeline

Giai đoạn build của pipeline được vận hành bởi AWS CodeBuild, một dịch vụ build được quản lý hoàn toàn chạy trên cloud. CodeBuild chịu trách nhiệm cho:

- Biên dịch mã nguồn của bạn

- Chạy các unit test hoặc integration test (nếu được cấu hình)

- Tạo ra các artifact output sẵn sàng cho việc triển khai

AWS CodeBuild sử dụng một file cấu hình có tên là **buildspec.yml**, được viết bằng định dạng YAML. File này định nghĩa các build phase, biến môi trường và các artifact output cho quá trình build.

Bạn có thể tham khảo tài liệu chính thức của AWS về buildspec.yml để có thông tin chi tiết.

### Phân tích Cấu trúc Buildspec

Dưới đây là tổng quan cấp cao về các stage được định nghĩa trong file buildspec.yml được sử dụng trong workshop này:

- install: Cài đặt các công cụ hoặc dependency cần thiết (nếu cần).

- pre_build:

  - Tải các biến môi trường

  - Đăng nhập vào Amazon ECR bằng AWS CLI

- build: Xây dựng Docker container image bằng Dockerfile trong repository của bạn.

- post_build: Đẩy image vừa được build lên repository Amazon ECR.

- artifacts: Định nghĩa các artifact output của build sẽ được chuyển đến stage tiếp theo của pipeline.
  Trong ví dụ của chúng ta, CodeBuild sẽ tạo và xuất ra các artifact sau:

  - **imagedefinitions.json**: Được sử dụng để cập nhật dịch vụ ECS với image mới

  - **imageDetail.json**: Chứa metadata về Docker image đã được build

  - **taskdef.json**: Định nghĩa task ECS được sử dụng để triển khai ứng dụng
