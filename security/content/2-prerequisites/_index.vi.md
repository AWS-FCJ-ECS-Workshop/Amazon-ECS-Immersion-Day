---
title: "Điều kiện tiên quyết"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: "<b> 2. </b>"
---

Để triển khai Cart service, trước tiên bạn phải tạo các tài nguyên AWS sau:

![alt text](/images/2-prerequisites/ECS-Lab-Networking-Prerequisites.png)

1. **Cart Security Group** – Security group kiểm soát lưu lượng truy cập đến từ UI Service đến Cart service thông qua các cổng được chỉ định.

2. **DynamoDB Table** – Cơ sở dữ liệu NoSQL serverless, được quản lý hoàn toàn để lưu trữ dữ liệu giỏ hàng với khả năng tự động mở rộng.

#### Các bước thiết lập chính

- [Tạo Cart Security Group](2.1-cart-security-group/) - Cấu hình quy tắc cho phép lưu lượng truy cập từ UI Service.

- [Tạo DynamoDB Table](2.2-create-dynamodb/) - Thiết lập bảng DynamoDB với chế độ on-demand capacity để lưu trữ dữ liệu giỏ hàng.