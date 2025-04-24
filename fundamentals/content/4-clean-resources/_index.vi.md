---
title: "Dọn dẹp tài nguyên"
date: "`r Sys.Date()`"
weight: 4
chapter: false
pre: "<b> 4. </b>"
---

{{% notice info %}}
Lưu ý: Để tiếp tục với các chương tiếp theo, bạn không cần phải xóa các tài nguyên đã tạo trong phần Fundamentals này.
{{% /notice %}}

2.1. Xóa IAM Roles

Xóa các IAM role sau:
- `retailStoreECSTaskExecutionRole`
- `retailStoreEcsTaskRole`

![alt text](/images/4-clean-resources/image.png)

2.2. Xóa Tài nguyên Mạng

Xóa tất cả cấu hình Virtual Private Cloud (VPC) và các thành phần mạng liên quan.

![alt text](/images/4-clean-resources/image-1.png)

2.3. Xóa Security Groups

Xóa các security group sau:
- `ecs-lab-public-sg`
- `ecs-lab-private-sg`
- `ecs-lab-ui-sg`

![alt text](/images/4-clean-resources/image-2.png)

2.4. Xóa Load Balancer

Xóa Application Load Balancer và các target group liên quan.

![alt text](/images/4-clean-resources/image-3.png)

2.5. Dọn dẹp Tài nguyên CloudWatch

Xóa tất cả CloudWatch logs, metrics và alarms đã tạo trong bài thực hành.

![alt text](/images/4-clean-resources/image-4.png)

2.6. Dọn dẹp Môi trường Lab

1. Kết thúc tất cả EC2 instance đã tạo cho bài thực hành này
![alt text](/images/4-clean-resources/image-5.png)

2. Xóa instance `ecs-lab-admin-ec2`
![alt text](/images/4-clean-resources/image-6.png)

3. Xóa các key pair liên quan nếu không còn cần thiết

2.7. Dọn dẹp Tài nguyên Mã nguồn

Xóa tất cả code repository và tài nguyên liên quan.

![alt text](/images/4-clean-resources/image-7.png)

2.8. Xóa UI Service

Điều hướng đến giao diện Amazon ECS và chọn UI service trong tab "Services". Nhấp vào "Delete" để bắt đầu quá trình xóa.

![alt text](/images/4-clean-resources/image-8.png)