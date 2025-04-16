---
title: "IAM Roles"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: "<b> 3. </b>"
---

{{% notice info %}}
Điều kiện tiên quyết: Hoàn thành các bài thực hành sau trước khi tiếp tục: [Fundamentals](/ecs-immersion-day/en-US/30-basic), [ECS Service Connect](/ecs-immersion-day/en-US/60-networking/ecs-service-connect)
{{% /notice %}}

### Tìm hiểu về IAM Roles trong Amazon ECS

Các task Amazon ECS có thể được gán IAM roles để cấp quyền cụ thể cho các container chạy trong task đó. Các role này cho phép tương tác an toàn giữa ứng dụng container của bạn và các dịch vụ AWS khác.

#### Task Execution Role

Task execution role cho phép Amazon ECS container agent thực hiện các hành động thay mặt bạn:

- Kéo container images từ Amazon ECR
- Ghi container logs vào Amazon CloudWatch Logs
- Lấy thông tin nhạy cảm từ AWS Secrets Manager (ví dụ: image pull secrets)

#### Task IAM Role

Task IAM roles cung cấp quyền trực tiếp cho mã ứng dụng của bạn chạy trong container:

- Tuân theo nguyên tắc đặc quyền tối thiểu
- Cho phép kiểm soát truy cập chi tiết đến các dịch vụ AWS
- Cung cấp sự tách biệt với EC2 instance roles

#### Trust Policy Bắt buộc

Khi tạo task IAM roles, triển khai trust policy sau để đảm bảo việc đảm nhận role phù hợp:

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

Trust policy này đảm bảo:
- Tasks có thể đảm nhận IAM roles được chỉ định
- Tách biệt role giữa tasks và EC2 instances
- Ranh giới bảo mật phù hợp

Để biết thêm hướng dẫn về bảo mật, tham khảo tài liệu [ECS Security Best Practices](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/security.html).