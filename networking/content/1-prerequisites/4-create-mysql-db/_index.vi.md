---
title: "Tạo Amazon Aurora MySQL Database"
date: "`r Sys.Date()`"
weight: 4
chapter: false
pre: "<b> 4. </b>"
---

Trong phần này, chúng ta sẽ tạo một Cơ sở dữ liệu Amazon Aurora MySQL mà ứng dụng catalog sẽ sử dụng để lưu trữ dữ liệu.

#### Tạo Database Subnet Group

1. Điều hướng đến [RDS Subnet Groups](https://console.aws.amazon.com/rds/home?db-subnet-groups-list:)
2. Chọn **Create DB Subnet Group**

![RDS Subnet Group Interface](/images/1-prerequisites/4-create-mysql-db/image.png)
*Hình 1: Giao diện RDS Subnet Group*

Cấu hình các thiết lập sau:
- Name: `ecs-lab-subnet-group-db`
- VPC: `ecs-lab-vpc`
- Availability Zones: Chọn cả `ap-southeast-2a` và `ap-southeast-2b`
- Subnets: Chọn `ecs-lab-subnet-private1-ap-southeast-2a` và `ecs-lab-subnet-private1-ap-southeast-2b`

![Create DB Subnet Group](/images/1-prerequisites/4-create-mysql-db/image-1.png)
*Hình 2: Cấu hình DB Subnet Group*

Sau khi tạo thành công, bạn sẽ thấy màn hình xác nhận:

![Subnet Group Creation Confirmation](/images/1-prerequisites/4-create-mysql-db/image-2.png)
*Hình 3: Tạo subnet group thành công*

#### Tạo Database Security Group

1. Điều hướng đến [VPC Security Groups](https://console.aws.amazon.com/vpcconsole/home#SecurityGroups:v=3)

![Security Group Dashboard](/images/1-prerequisites/4-create-mysql-db/image-3.png)
*Hình 4: Bảng điều khiển Security Group*

Cấu hình các thiết lập sau:
- Security group name: `ecs-lab-db-sg`
- VPC: `ecs-lab-vpc`
- Inbound Rules: Thêm rule cho TCP port 3306 từ nguồn `ecs-lab-catalog-sg`

![Create Security Group](/images/1-prerequisites/4-create-mysql-db/image-4.png)
*Hình 5: Cấu hình Security Group*

![Security Group Creation Confirmation](/images/1-prerequisites/4-create-mysql-db/image-5.png)
*Hình 6: Tạo security group thành công*

#### Tạo Cơ sở dữ liệu Amazon Aurora MySQL

1. Điều hướng đến [Amazon RDS console](https://console.aws.amazon.com/rds/home2)

![RDS Dashboard](/images/1-prerequisites/4-create-mysql-db/image-6.png)
*Hình 7: Giao diện Aurora và RDS Database*

Cấu hình cơ sở dữ liệu:
1. Chọn **Standard create**
2. Chọn **Aurora (MySQL Compatible)** làm loại engine

![Create Database](/images/1-prerequisites/4-create-mysql-db/image-7.png)
*Hình 8: Giao diện tạo Database*

Cấu hình thiết lập database:
- Template: **Dev/Test**
- DB cluster identifier: Nhập tên bạn muốn
- Master username: `admin`
- Master password: `admin123`

![Database Settings](/images/1-prerequisites/4-create-mysql-db/image-8.png)
*Hình 9: Thiết lập cấu hình Database*

Cấu hình kết nối:
- VPC: `ecs-lab-vpc`
- DB Subnet group: `ecs-lab-subnet-group-db`
- Security group: `ecs-lab-db-sg`
- Availability Zone: `ap-southeast-2a`

![Connectivity Settings](/images/1-prerequisites/4-create-mysql-db/image-9.png)
*Hình 10: Cấu hình kết nối*

Trong Additional Configuration:
- Initial database name: `catalog`
- Giữ các thiết lập khác mặc định

![Initial Database Configuration](/images/1-prerequisites/4-create-mysql-db/image-10.png)
*Hình 11: Cấu hình database ban đầu*

✅ Bạn đã tạo thành công Cơ sở dữ liệu Aurora MySQL cho ứng dụng catalog của mình.