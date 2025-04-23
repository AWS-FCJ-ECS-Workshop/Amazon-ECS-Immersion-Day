---
title: "Creating Amazon Aurora MySQL Database"
date: "`r Sys.Date()`"
weight: 4
chapter: false
pre: "<b> 4. </b>"
---

In this section, we will create an Amazon Aurora MySQL Database that the catalog application will use for data storage.

#### Create a Database Subnet Group

1. Navigate to [RDS Subnet Groups](https://console.aws.amazon.com/rds/home?db-subnet-groups-list:)
2. Choose **Create DB Subnet Group**

![RDS Subnet Group Interface](/images/1-prerequisites/4-create-mysql-db/image.png)
*Figure 1: RDS Subnet Group console*

Configure the following settings:
- Name: `ecs-lab-subnet-group-db`
- VPC: `ecs-lab-vpc`
- Availability Zones: Select both `ap-southeast-2a` and `ap-southeast-2b`
- Subnets: Choose `ecs-lab-subnet-private1-ap-southeast-2a` and `ecs-lab-subnet-private1-ap-southeast-2b`

![Create DB Subnet Group](/images/1-prerequisites/4-create-mysql-db/image-1.png)
*Figure 2: DB Subnet Group configuration*

After successful creation, you will see the confirmation screen:

![Subnet Group Creation Confirmation](/images/1-prerequisites/4-create-mysql-db/image-2.png)
*Figure 3: Successful subnet group creation*

#### Create a Database Security Group

1. Navigate to [VPC Security Groups](https://console.aws.amazon.com/vpcconsole/home#SecurityGroups:v=3)

![Security Group Dashboard](/images/1-prerequisites/4-create-mysql-db/image-3.png)
*Figure 4: Security Group dashboard*

Configure the following settings:
- Security group name: `ecs-lab-db-sg`
- VPC: `ecs-lab-vpc`
- Inbound Rules: Add rule for TCP port 3306 from source `ecs-lab-catalog-sg`

![Create Security Group](/images/1-prerequisites/4-create-mysql-db/image-4.png)
*Figure 5: Security Group configuration*

![Security Group Creation Confirmation](/images/1-prerequisites/4-create-mysql-db/image-5.png)
*Figure 6: Successful security group creation*

#### Create an Amazon Aurora MySQL Database

1. Navigate to the [Amazon RDS console](https://console.aws.amazon.com/rds/home2)

![RDS Dashboard](/images/1-prerequisites/4-create-mysql-db/image-6.png)
*Figure 7: Aurora and RDS Database console*

Configure the database:
1. Choose **Standard create**
2. Select **Aurora (MySQL Compatible)** as the engine type

![Create Database](/images/1-prerequisites/4-create-mysql-db/image-7.png)
*Figure 8: Database creation interface*

Configure database settings:
- Template: **Dev/Test**
- DB cluster identifier: Enter your preferred name
- Master username: `admin`
- Master password: `admin123`

![Database Settings](/images/1-prerequisites/4-create-mysql-db/image-8.png)
*Figure 9: Database configuration settings*

Configure connectivity:
- VPC: `ecs-lab-vpc`
- DB Subnet group: `ecs-lab-subnet-group-db`
- Security group: `ecs-lab-db-sg`
- Availability Zone: `ap-southeast-2a`

![Connectivity Settings](/images/1-prerequisites/4-create-mysql-db/image-9.png)
*Figure 10: Connectivity configuration*

Under Additional Configuration:
- Initial database name: `catalog`
- Keep all other settings as default

![Initial Database Configuration](/images/1-prerequisites/4-create-mysql-db/image-10.png)
*Figure 11: Initial database configuration*

✅ You have successfully created an Aurora MySQL Database for your catalog application.