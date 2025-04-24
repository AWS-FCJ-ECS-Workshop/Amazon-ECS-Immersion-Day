---
title: "Dọn dẹp tài nguyên"
date: "`r Sys.Date()`"
weight: 4
chapter: false
pre: "<b> 4. </b>"
---

{{% notice info %}}
Quan trọng: Phần Clean Up Resources này sẽ chỉ xóa các dịch vụ được tạo trong phần này.
Để xóa tất cả tài nguyên, bạn phải xóa chúng theo thứ tự bắt đầu từ phần [Fundamentals](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/fundamentals/).
{{% /notice %}}

#### Xóa Security Group của EFS

Điều hướng đến Dashboard Security Groups:
1. Chọn `ecs-lab-efs-sg`
2. Nhấp **Delete**

![alt text](/images/4-clean-resources/image.png)
*Hình 1: Xóa Security Group của EFS*

#### Xóa EFS

Điều hướng đến Dashboard EFS:
1. Chọn `ecs-lab-assets-efs`
2. Nhấp **Delete**

![alt text](/images/4-clean-resources/image-1.png)
*Hình 2: Xóa Elastic File System*

