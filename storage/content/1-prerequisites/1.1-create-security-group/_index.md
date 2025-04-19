---
title: "Creating Security Group"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: "<b> 1. </b>"
---

![alt text](/images/1-prerequisites/1.1-create-security-group/ECS-Lab-Networking-Storage-1.png)
*Figure 1. Security group architecture*

#### Create Security Group

1. Access the [Security Groups console](https://console.aws.amazon.com/ec2/home?#SecurityGroups:)

2. Configure the following settings in the Create Security Group page:

   - Security group name: `ecs-lab-efs-sg`
   - VPC: Select `ecs-lab-vpc`
   - Inbound rules: 
     - Type: `NFS`
     - Source: `ecs-lab-asset-sg`

![alt text](/images/1-prerequisites/1.1-create-security-group/image-1.png)
*Figure 2. Create Security Group interface*

3. Review the configuration and click **Create security group**

After successful creation, you will see the new security group in the console:

![alt text](/images/1-prerequisites/1.1-create-security-group/image.png)
*Figure 3. Security Group created successfully*