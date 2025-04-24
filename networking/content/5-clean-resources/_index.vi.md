---
title: "Dọn dẹp tài nguyên"
date: "`r Sys.Date()`"
weight: 5
chapter: false
pre: "<b> 5. </b>"
---

{{% notice info %}}
Quan trọng: Phần Clean Up Resources này sẽ chỉ xóa các dịch vụ được tạo trong phần này.
Để xóa tất cả tài nguyên, bạn phải xóa chúng theo thứ tự bắt đầu từ phần [Fundamentals](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/fundamentals/).
{{% /notice %}}

Hướng dẫn này giúp bạn dọn dẹp tất cả tài nguyên đã tạo trong workshop để tránh phát sinh chi phí.

#### Security Groups
1. Xóa Cart Security Group (`ecs-lab-cart-sg`)
![Delete Cart Security Group](/images/5-clean-resources/image.png)

2. Xóa Asset và Catalog Security Groups
![Delete Asset Security Group](/images/5-clean-resources/image-4.png)
![Delete Catalog Security Group](/images/5-clean-resources/image-5.png)

#### Tài nguyên Database
1. Xóa các bảng DynamoDB
![Delete DynamoDB](/images/5-clean-resources/image-1.png)

2. Xóa cài đặt MySQL Aurora Database
![Delete Aurora Database](/images/5-clean-resources/image-8.png)

#### Tài nguyên Service
1. Xóa Cart Services
![Delete Cart Services](/images/5-clean-resources/image-2.png)

2. Xóa Assets & Catalog Services
![Delete Assets and Catalog](/images/5-clean-resources/image-13.png)

#### Tài nguyên Network
1. Xóa Cloud Map Namespaces
![Delete Cloud Map](/images/5-clean-resources/image-6.png)

2. Xóa Target Group có hỗ trợ TLS cho UI
![Delete Target Group](/images/5-clean-resources/image-12.png)

#### Tài nguyên Security
1. Tắt Amazon GuardDuty
![Disable GuardDuty](/images/5-clean-resources/image-3.png)

2. Xóa cấu hình Catalog IAM Role
![Delete IAM Role](/images/5-clean-resources/image-7.png)

3. Xóa AWS Private Certificate Authority
![Delete Private CA](/images/5-clean-resources/image-10.png)

4. Xóa chứng chỉ AWS Certificate Manager
![Delete ACM Certificates](/images/5-clean-resources/image-11.png)

#### Tài nguyên Parameter Store
Xóa các biến Secret Manager bằng cách chạy:
```bash
aws ssm delete-parameter \
--name /retail-store-ecs/catalog/db-endpoint
```
![Delete Parameters](/images/5-clean-resources/image-9.png)

Kiểm tra lại để đảm bảo tất cả tài nguyên đã được xóa đúng cách để tránh phát sinh chi phí không mong muốn trên tài khoản AWS của bạn.