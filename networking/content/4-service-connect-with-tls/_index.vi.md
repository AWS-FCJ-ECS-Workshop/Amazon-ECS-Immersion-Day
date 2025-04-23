---
title: "Amazon ECS Service Connect với TLS"
date: "`r Sys.Date()`"
weight: 4
chapter: false
pre: "<b> 4. </b>"
---

{{% notice info %}}
Hoàn thành các điều kiện tiên quyết sau trước khi bắt đầu bài thực hành này: [Fundamentals](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/fundamentals/), [ECS Service Connect](../3-service-connect/)
{{% /notice %}}

Amazon ECS Service Connect cung cấp tính năng mã hóa lưu lượng tích hợp sử dụng chứng chỉ Transport Layer Security (TLS) cho các service ECS. Khi bạn cấu hình service ECS với AWS Private Certificate Authority (AWS Private CA), ECS tự động quản lý chứng chỉ TLS để mã hóa lưu lượng giữa các service được kích hoạt Service Connect. Điều này bao gồm:

- Cấp phát chứng chỉ
- Luân chuyển chứng chỉ
- Phân phối chứng chỉ  
- Mã hóa lưu lượng giữa các service

Trong bài thực hành này, bạn sẽ:

- Triển khai phiên bản UI service có hỗ trợ TLS (`ui-tls`)
- Cấu hình mã hóa TLS cho Catalog và Assets service hiện có
- Xác thực kết nối TLS giữa các service

[Tìm hiểu thêm về ECS Service Connect TLS](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/service-connect-tls.html)