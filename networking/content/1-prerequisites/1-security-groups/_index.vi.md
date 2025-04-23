---
title: "Security Groups cho Service"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: "<b> 1. </b>"
---

Trong phần này, chúng ta sẽ tạo Security Groups cho các service Assets và Catalog để cho phép giao tiếp với Security Group của UI.

#### Tạo Security Group cho Assets

Điều hướng đến [Security Groups](https://console.aws.amazon.com/ec2/home?SecurityGroups:) > Create security group

Cấu hình các thiết lập sau:
- Security group name: `ecs-lab-asset-sg`
- VPC: `ecs-lab-vpc`
- Inbound rules: Cho phép TCP port 8080 từ `ecs-lab-ui-sg`
- Outbound rules: Cho phép tất cả traffic

![alt text](/images/1-prerequisites/1-security-groups/image.png)
*Hình 1: Cấu hình Security Group cho Assets*

![alt text](/images/1-prerequisites/1-security-groups/image-1.png)
*Hình 2: Đã tạo thành công Security Group cho Assets*

---

#### Tạo Security Group cho Catalog

Điều hướng đến [Security Groups](https://console.aws.amazon.com/ec2/home?SecurityGroups:) > Create security group

Cấu hình các thiết lập sau:
- Security group name: `ecs-lab-catalog-sg`
- VPC: `ecs-lab-vpc`
- Inbound rules: Cho phép TCP port 8080 từ `ecs-lab-ui-sg`
- Outbound rules: Cho phép tất cả traffic

![alt text](/images/1-prerequisites/1-security-groups/image-2.png)
*Hình 3: Cấu hình Security Group cho Catalog*

![alt text](/images/1-prerequisites/1-security-groups/image-3.png)
*Hình 4: Đã tạo thành công Security Group cho Catalog*

Bạn đã tạo thành công Security Groups cho cả service Assets và Catalog.