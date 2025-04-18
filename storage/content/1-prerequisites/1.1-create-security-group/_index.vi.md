---
title: "Điều kiện tiên quyết"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: "<b> 1. </b>"
---



Trước khi tiếp tục với các bài thực hành phần Storage, chúng ta cần tạo security group cho EFS để cho phép Asset truy cập vào EFS File System, và gắn policy cần thiết vào ECS IAM Role để cho phép ECS Cluster quản lý hệ thống tệp EFS.

![alt text](/images/1-prerequisites/ECS-Lab-Networking-Storage-1.png)
*Hình 1. Kiến trúc Storage*

#### Tạo Security Group

Điều hướng đến [Security Group Dashboard](https://console.aws.amazon.com/ec2/home?#SecurityGroups:)

Trong giao diện Create Security Group:
- Nhập tên security group `ecs-lab-efs-sg`
- Chọn VPC `ecs-lab-vpc`
- Thêm Inbound rule: Type `NFS` - Source `ecs-lab-asset-sg`

![alt text](/images/1-prerequisites/image-1.png)
*Hình 2. Giao diện tạo Security Group*

Kết quả:

![alt text](/images/1-prerequisites/image.png)
*Hình 3. Tạo Security Group thành công*

#### Tạo EFS File System

1. Điều hướng đến [EFS Dashboard](https://console.aws.amazon.com/efs/home/file-systems) > Chọn Create File System

![alt text](/images/1-prerequisites/image-2.png)
*Hình 4. Giao diện EFS Dashboard*

2. Trong File System Settings > Nhập tên, giữ các tùy chọn mặc định

![alt text](/images/1-prerequisites/image-3.png)
*Hình 5. Giao diện File System Settings*

3. Trong Network Access, chọn VPC `ecs-lab-vpc`, cấu hình mount targets trong 2 AZ với `ecs-lab-efs-sg`

![alt text](/images/1-prerequisites/image-4.png)
*Hình 6. Cấu hình Network Access*

4. Hoàn tất tạo EFS file system

![alt text](/images/1-prerequisites/image-5.png)
*Hình 7. Tạo EFS File System thành công*

#### Gắn Policy vào ECS IAM Role

1. Điều hướng đến [giao diện IAM Role](console.aws.amazon.com/iam/home?roles) và chọn `retailStoreECSTaskRole`

![alt text](/images/1-prerequisites/image-6.png)
*Hình 8. Giao diện IAM Role*

2. Thêm policy vào IAM Role

![alt text](/images/1-prerequisites/image-7.png)
*Hình 9. Thêm policy vào IAM Role*

3. Tìm kiếm và chọn `AmazonEFSCSIDriverPolicy` > Add Permissions

![alt text](/images/1-prerequisites/image-8.png)
*Hình 10. Giao diện gắn policy*

4. Gắn policy thành công:

![alt text](/images/1-prerequisites/image-9.png)
*Hình 11. Gắn policy thành công*