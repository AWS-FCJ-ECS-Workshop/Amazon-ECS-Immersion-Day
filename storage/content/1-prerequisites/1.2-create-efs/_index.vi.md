---
title: "Tạo File System EFS"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: "<b> 2. </b>"
---

#### AWS Elastic File System là gì?

Amazon Elastic File System (EFS) là một dịch vụ lưu trữ tập tin có khả năng mở rộng và được quản lý hoàn toàn, cho phép bạn chia sẻ dữ liệu tập tin đồng thời trên nhiều instance Amazon EC2. Nó cung cấp một hệ thống tập tin đơn giản, serverless và linh hoạt tự động tăng và giảm khi bạn thêm và xóa tập tin.

#### EFS hoạt động như thế nào?

Amazon EFS tạo và quản lý các hệ thống tập tin có thể được truy cập đồng thời bởi nhiều instance EC2 trên các Availability Zone khác nhau trong một region. Kiến trúc phân tán này đảm bảo tính sẵn sàng cao và độ bền của dữ liệu của bạn.

Để sử dụng EFS, bạn phải tạo các mount target trong VPC để kết nối giữa các instance EC2 và hệ thống tập tin EFS. Mount target hoạt động như các điểm truy cập mà ứng dụng của bạn có thể sử dụng để kết nối với hệ thống tập tin.

![How does EFS Work?](/images/2-efs-volume-with-fargate/image.png)
*Hình 1. EFS hoạt động như thế nào?*

#### Tạo File System EFS

1. Truy cập [EFS Dashboard](https://console.aws.amazon.com/efs/home/file-systems) và chọn **Create File System**

![alt text](/images/1-prerequisites/image-2.png)
*Hình 4. Giao diện EFS Dashboard*

2. Cấu hình File System Settings:
   - Nhập tên mô tả cho hệ thống tập tin của bạn
   - Giữ các tùy chọn cấu hình mặc định

![alt text](/images/1-prerequisites/image-3.png)
*Hình 5. Giao diện File System Settings*

3. Cấu hình Network Access:
   - Chọn VPC: `ecs-lab-vpc`
   - Cấu hình mount target trong hai Availability Zone
   - Chọn security group: `ecs-lab-efs-sg`

![alt text](/images/1-prerequisites/image-4.png)
*Hình 6. Cấu hình Network Access*

4. Xác nhận việc tạo file system EFS

![alt text](/images/1-prerequisites/image-5.png)
*Hình 7. EFS File System đã được tạo thành công*

#### Gắn Policy vào ECS IAM Role

1. Điều hướng đến [IAM Role console](console.aws.amazon.com/iam/home?roles) và chọn `retailStoreECSTaskRole`

![alt text](/images/1-prerequisites/image-6.png)
*Hình 8. Giao diện IAM Role*

2. Chọn **Add permissions**

![alt text](/images/1-prerequisites/image-7.png)
*Hình 9. Thêm policy vào IAM Role*

3. Gắn policy cần thiết:
   - Tìm kiếm `AmazonEFSCSIDriverPolicy`
   - Chọn policy
   - Chọn **Add permissions**

![alt text](/images/1-prerequisites/image-8.png)
*Hình 10. Giao diện gắn policy*

4. Xác nhận việc gắn policy

![alt text](/images/1-prerequisites/image-9.png)
*Hình 11. Policy đã được gắn thành công*