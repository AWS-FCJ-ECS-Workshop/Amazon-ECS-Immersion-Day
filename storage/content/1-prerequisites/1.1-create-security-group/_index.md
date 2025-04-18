---
title: "Creating secuity group"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: "<b> 1. </b>"
---

![alt text](/images/1-prerequisites/1.1-create-security-group/ECS-Lab-Networking-Storage-1.png)
*Figure 1. Security group architecture*

#### Create Security Group

Navigate to [Security Group Dashboard](https://console.aws.amazon.com/ec2/home?#SecurityGroups:)

In the Create Security Group interface:
- Enter a security group name `ecs-lab-efs-sg`
- Select VPC `ecs-lab-vpc`
- Add Inbound rule: Type `NFS` - Source `ecs-lab-asset-sg`

![alt text](/images/1-prerequisites/1.1-create-security-group/image-1.png)
*Figure 2. Create Security Group interface*

Result:

![alt text](/images/1-prerequisites/1.1-create-security-group/image.png)
*Figure 3. Security Group created successfully*