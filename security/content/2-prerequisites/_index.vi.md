---
title: "Prerequisites"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: "<b> 2. </b>"
---

Trước khi bắt đầu lab, chúng ta cần tạo **các tài nguyên AWS cần thiết** cho dịch vụ Cart:

1. **Cart Security Group** – Nhóm bảo mật này sẽ cho phép lưu lượng qua các cổng cần thiết cho **Application Load Balancer**, đảm bảo định tuyến chính xác đến các dịch vụ liên quan đến giỏ hàng.

2. **Serverless DynamoDB** – Cơ sở dữ liệu không máy chủ, được quản lý hoàn toàn để **lưu trữ và truy xuất thông tin giỏ hàng**, giúp truy cập nhanh và mở rộng dễ dàng.

---

#### Các bước cấu hình chính

- 🔐 [Tạo Cart Security Group](2.1-cart-security-group/)  
  Thiết lập quy tắc inbound để mở cổng cần thiết cho Application Load Balancer.

- 🗃️ [Tạo Serverless DynamoDB](2.2-create-dynamodb/)  
  Tạo bảng DynamoDB để lưu trữ dữ liệu giỏ hàng, hỗ trợ mô hình tính phí theo lượt truy vấn.
