---
title: "Mô hình chia sẻ trách nhiệm"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: "<b> 1. </b>"
---

Mô hình chia sẻ trách nhiệm - AWS xác định trách nhiệm bảo mật giữa AWS và khách hàng khi sử dụng Amazon Elastic Container Service (Amazon ECS). Mô hình này phân biệt giữa:

* Trách nhiệm của AWS - Bảo mật "của" cloud
* Trách nhiệm của khách hàng - Bảo mật "trong" cloud

AWS duy trì toàn quyền kiểm soát bảo mật của control plane Amazon ECS và cơ sở hạ tầng bên dưới để đảm bảo độ tin cậy và bảo mật của dịch vụ. Là khách hàng, bạn chịu trách nhiệm về:

* Bảo mật dữ liệu
* Bảo mật mạng
* Bảo mật runtime
* Ghi log và giám sát
* Quản lý truy cập
* Cấu hình tài nguyên

![ECS EC2](/images/1-shared-responsibility-model/image.png)
*Hình 1. Mô hình chia sẻ trách nhiệm AWS cho Amazon ECS với EC2*

Khi sử dụng AWS Fargate với Amazon ECS, AWS đảm nhận thêm các trách nhiệm bảo mật so với EC2 instances:

* Quản lý cơ sở hạ tầng cloud nền tảng
* Bảo mật môi trường runtime cho việc thực thi task
* Tự động mở rộng cơ sở hạ tầng
* Bảo trì và vá lỗi hệ điều hành

![ECS Fargate](/images/1-shared-responsibility-model/image-1.png)
*Hình 2. Mô hình chia sẻ trách nhiệm AWS cho Amazon ECS với Fargate*

Tìm hiểu thêm về trách nhiệm bảo mật bằng cách truy cập tài liệu chính thức [AWS Shared Responsibility Model](https://aws.amazon.com/compliance/shared-responsibility-model).