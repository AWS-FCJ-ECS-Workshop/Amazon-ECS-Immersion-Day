---
title: "Khởi tạo Security Group cho Cart"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: "<b> 2.1. </b>"
---

Cấu hình security group để cho phép giao tiếp mạng giữa UI Service và Cart Service thông qua cổng 8080.

#### Tạo Security Group cho Cart

1. Mở Amazon EC2 console và chọn **Security Groups** từ thanh điều hướng bên trái

2. Chọn **Create security group**

3. Nhập các thông tin cấu hình sau:
   - Security group name: `ecs-lab-cart-sg`
   - Description: Security group cho Cart service
   - VPC: `ecs-lab-vpc`
   - Inbound rules:
     - Type: Custom TCP
     - Port range: 8080
     - Source: `ecs-lab-ui-sg` (UI Security Group)
   - Outbound rules: Giữ mặc định (Cho phép tất cả traffic)

![alt text](/images/2-prerequisites/2.1-cart-security-group/image.png)
*Hình 2: Cấu hình security group cho Cart*

4. Chọn **Create security group**

Sau khi tạo thành công, security group mới sẽ xuất hiện trong danh sách security groups:

![alt text](/images/2-prerequisites/2.1-cart-security-group/image-1.png)
*Hình 3: Xác nhận tạo security group*

Security group của Cart quản lý kiểm soát truy cập mạng cho các container Cart service chạy trong Amazon ECS cluster. Nó đảm bảo giao tiếp an toàn giữa các service trong khi vẫn duy trì sự cách ly cần thiết.