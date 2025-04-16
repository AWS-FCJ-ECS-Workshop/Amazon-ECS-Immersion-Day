---
title: "Initialize Cart Security Group"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: "<b> 2.1. </b>"
---

To enable communication between the UI Service and Cart Service, you need to configure the security group to accept inbound traffic on port 8080, which is the application's exposed port.

#### Create Cart Security Group

1. Navigate to the **Security Groups** page in the Amazon EC2 console

2. Choose **Create security group**

3. Configure the following settings:
   - Security group name: `ecs-lab-cart-sg`
   - Description: Security group for Cart service
   - VPC: Select `ecs-lab-vpc`
   - Inbound rules:
     - Type: Custom TCP
     - Port range: 8080
     - Source: Select security group `ecs-lab-ui-sg` (UI Security Group)
   - Outbound rules: Keep default (Allow all)

![alt text](/images/2-prerequisites/2.1-cart-security-group/image.png)
*Figure 2: Cart security group configuration*

4. Choose **Create security group**

After successful creation, you will see the new security group in the list:

![alt text](/images/2-prerequisites/2.1-cart-security-group/image-1.png)
*Figure 3: Security group creation confirmation*

This security group will control network access to the Cart service containers running in your ECS cluster.