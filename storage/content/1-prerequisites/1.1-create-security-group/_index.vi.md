---
title: "Tạo Security Group"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: "<b> 1. </b>"
---

![alt text](/images/1-prerequisites/1.1-create-security-group/ECS-Lab-Networking-Storage-1.png)
*Hình 1. Kiến trúc security group*

#### Tạo Security Group

1. Truy cập vào [Security Groups console](https://console.aws.amazon.com/ec2/home?#SecurityGroups:)

2. Cấu hình các thông số sau trong trang Create Security Group:

   - Security group name: `ecs-lab-efs-sg`
   - VPC: Chọn `ecs-lab-vpc`
   - Inbound rules: 
     - Type: `NFS`
     - Source: `ecs-lab-asset-sg`

![alt text](/images/1-prerequisites/1.1-create-security-group/image-1.png)
*Hình 2. Giao diện tạo Security Group*

3. Xem lại cấu hình và nhấn **Create security group**

Sau khi tạo thành công, bạn sẽ thấy security group mới trong console:

![alt text](/images/1-prerequisites/1.1-create-security-group/image.png)
*Hình 3. Security Group đã được tạo thành công*