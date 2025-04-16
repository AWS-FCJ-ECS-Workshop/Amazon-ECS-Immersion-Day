---
title: "Khởi tạo Security Group cho Cart"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: "<b> 2.1. </b>"
---

Để cho phép giao tiếp giữa Application Load Balancer và Cart Service, bạn cần cấu hình security group để chấp nhận lưu lượng truy cập vào qua cổng 8080, là cổng được ứng dụng mở.

#### Tạo Security Group cho Cart

1. Điều hướng đến trang **Security Groups** trong Amazon EC2 console

2. Chọn **Create security group**

3. Cấu hình các thông số sau:
   - Security group name: `ecs-lab-cart-sg`
   - Description: Security group cho Cart service
   - VPC: Chọn `ecs-lab-vpc`
   - Inbound rules:
     - Type: Custom TCP
     - Port range: 8080
     - Source: Chọn security group `ecs-lab-public-sg` (ALB Security Group)
   - Outbound rules: Giữ mặc định (Allow all)

![alt text](image.png)
*Hình 2: Cấu hình security group cho Cart*

4. Chọn **Create security group**

Sau khi tạo thành công, bạn sẽ thấy security group mới trong danh sách:

![alt text](image-1.png)
*Hình 3: Xác nhận tạo security group thành công*

Security group này sẽ kiểm soát quyền truy cập mạng đến các container Cart service chạy trong ECS cluster của bạn.