+++
title = "Prerequisites"
date = 2020-05-14T00:38:32+07:00
weight = 2
chapter = false
pre = "<b>2. </b>"
+++

### Điều kiện tiên quyết cho CI/CD Pipeline

Trong lab này, chúng ta sẽ cấu hình triển khai Amazon ECS mặc định bằng AWS CodePipeline.

Quá trình thiết lập bao gồm việc tạo các tài nguyên sau:

- Một kho lưu trữ Github để chứa mã nguồn của Ứng dụng Cửa hàng Bán lẻ (Vui lòng tham khảo phần [**Fundamentals**](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/fundamentals/) để lấy mã nguồn\_).

- Một Amazon Elastic Container Registry (ECR) để lưu trữ các Docker image của ứng dụng (Vui lòng tham khảo phần [**Fundamentals**](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/fundamentals/) để tạo ECR\_).

- Một Amazon S3 bucket để đóng vai trò là kho lưu trữ artifact cho pipeline.

- Một dự án AWS CodeBuild để biên dịch mã nguồn và xây dựng Docker image.

- Một AWS CodePipeline để tự động hóa và điều phối quy trình triển khai.

- Một tham số AWS Systems Manager Parameter Store để theo dõi ID build mới nhất.

- Một bản sao cục bộ của kho lưu trữ Cửa hàng Bán lẻ để chỉnh sửa và commit các thay đổi mã.
