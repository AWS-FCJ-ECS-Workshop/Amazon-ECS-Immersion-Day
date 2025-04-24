---
title: "IAM Roles"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: "<b> 3. </b>"
---

{{% notice info %}}
Điều kiện tiên quyết: Hoàn thành các bài thực hành sau trước khi tiếp tục: [Fundamentals](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/fundamentals/), [ECS Service Connect](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/networking/3-service-connect/)
{{% /notice %}}

#### Tìm hiểu về IAM Roles trong Amazon ECS

![IAM-Role](/images/3-iam-roles/ECS-Lab-Networking-IAM-Role.png)
*Hình 1. Sơ đồ IAM Roles*

Amazon ECS sử dụng hai IAM role riêng biệt để quản lý quyền cho các ứng dụng container: Task Execution Role và Task IAM Role. Mỗi role phục vụ một mục đích cụ thể trong việc bảo mật khối lượng công việc container và tương tác của chúng với các dịch vụ AWS.

#### Task Execution Role

Task Execution Role cấp quyền cho Amazon ECS container agent thực hiện các hoạt động quản lý container thiết yếu:

- Pull container image từ Amazon Elastic Container Registry (ECR)
- Xuất bản container logs tới Amazon CloudWatch Logs
- Truy xuất dữ liệu nhạy cảm từ AWS Secrets Manager hoặc AWS Systems Manager Parameter Store

Nếu không có Task Execution Role được cấu hình đúng cách, ECS agent không thể thực hiện các hoạt động cơ bản này, có thể ngăn container của bạn khởi động thành công.

#### Task IAM Role

Task IAM Role cung cấp quyền trực tiếp cho mã ứng dụng chạy trong container:

- Kiểm soát quyền truy cập vào các dịch vụ và tài nguyên AWS mà ứng dụng của bạn cần
- Thực hiện nguyên tắc đặc quyền tối thiểu bằng cách giới hạn quyền chỉ ở mức cần thiết
- Duy trì cách ly bảo mật giữa các task khác nhau và các EC2 instance cơ bản
- Cho phép kiểm soát truy cập chi tiết thông qua IAM policies

#### Trust Policy Bắt buộc

Để cho phép ECS tasks đảm nhận IAM role được chỉ định, hãy triển khai trust policy này:

```json
{
   "Version": "2012-10-17",
   "Statement": [
      {
         "Sid": "",
         "Effect": "Allow",
         "Principal": {
         "Service": "ecs-tasks.amazonaws.com"
         },
         "Action": "sts:AssumeRole"
      }
   ]
}
```

Cấu hình trust policy này:
- Thiết lập mối quan hệ tin cậy cần thiết giữa ECS tasks và IAM roles
- Đảm bảo ranh giới bảo mật phù hợp giữa các thành phần khác nhau
- Cho phép đảm nhận role an toàn bởi ECS task runtime

Xem tài liệu [ECS Security Best Practices](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/security.html) để được hướng dẫn bảo mật toàn diện.