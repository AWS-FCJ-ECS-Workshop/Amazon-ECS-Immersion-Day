---
title : "Amazon ECS Series: Security"
date :  "`r Sys.Date()`" 
chapter : false
---

# Security

{{% notice info %}}
Điều kiện tiên quyết: Hoàn thành các chương [Fundamentals](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/fundamentals/) và [ECS Service Connect](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/networking/) trước khi bắt đầu bài thực hành này.
{{% /notice %}}

Amazon ECS Fargate cung cấp khả năng điều phối container serverless giúp loại bỏ gánh nặng quản lý cơ sở hạ tầng. Workshop này hướng dẫn bạn triển khai các biện pháp kiểm soát bảo mật toàn diện cho ứng dụng container của bạn trên ECS Fargate, bao gồm các khía cạnh quan trọng như:
- Cấu hình bảo mật mạng
- Quản lý định danh và truy cập
- Xử lý bảo mật thông tin nhạy cảm
- Yêu cầu tuân thủ
- Giám sát bảo mật runtime

#### Ước tính chi phí

| Dịch vụ              | Khu vực                 | Chi phí ban đầu | Hàng tháng | Tổng 12 tháng | Tiền tệ | Tóm tắt cấu hình |
|-----------------------|-------------------------|-----------------|------------|----------------|----------|------------------------|
| AWS Fargate           | Châu Á Thái Bình Dương (Sydney) | 0             | 0.45       | 5.40           | USD      | Linux, x86, 15 phút mỗi task hàng ngày, 20 GB lưu trữ, 2 GB bộ nhớ |
| DynamoDB On-Demand    | Châu Á Thái Bình Dương (Sydney) | 0             | 2.85       | 34.20          | USD      | Bảng tiêu chuẩn, item 5 KB, 10 GB lưu trữ |
| Amazon GuardDuty      | Châu Á Thái Bình Dương (Sydney) | -             | -          | -              | USD      | Giám sát runtime container |
| **Tổng cộng**        |                         | **0**          | **3.30**   | **39.60**      | USD      |                        |

#### Các module Workshop

1. [Mô hình chia sẻ trách nhiệm](1-shared-responsibility-model/)
2. [Điều kiện tiên quyết](2-prerequisites/)
3. [IAM Roles](3-iam-roles/)
4. [Amazon GuardDuty](4-amazon-guardduty/)
5. [Dọn dẹp tài nguyên](5-clean-resources/)
6. [Kết luận](6-conclusion/)