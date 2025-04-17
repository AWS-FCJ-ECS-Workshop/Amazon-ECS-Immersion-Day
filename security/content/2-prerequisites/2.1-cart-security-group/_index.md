---
title: "Initialize Cart Security Group"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: "<b> 2.1. </b>"
---

Configure the security group to enable network communication between the UI Service and Cart Service through port 8080.

#### Create Cart Security Group

1. Open the Amazon EC2 console and select **Security Groups** from the left navigation pane

2. Select **Create security group**

3. Enter the following configuration details:
   - Security group name: `ecs-lab-cart-sg`
   - Description: Security group for Cart service
   - VPC: `ecs-lab-vpc`
   - Inbound rules:
     - Type: Custom TCP
     - Port range: 8080
     - Source: `ecs-lab-ui-sg` (UI Security Group)
   - Outbound rules: Leave as default (Allow all traffic)

![alt text](/images/2-prerequisites/2.1-cart-security-group/image.png)
*Figure 2: Cart security group configuration*

4. Select **Create security group**

Upon successful creation, the new security group appears in the security groups list:

![alt text](/images/2-prerequisites/2.1-cart-security-group/image-1.png)
*Figure 3: Security group creation confirmation*

The Cart security group manages network access controls for Cart service containers running within your Amazon ECS cluster. It ensures secure communication between services while maintaining necessary isolation.