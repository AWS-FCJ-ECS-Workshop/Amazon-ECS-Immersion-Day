---
title: "Service Security Groups"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: "<b> 1. </b>"
---

In this section, we will create Security Groups for the Assets and Catalog services to enable communication with the UI Security Group.

Navigate to [Security Groups](https://console.aws.amazon.com/ec2/home?SecurityGroups:) > Create security group

Configure the following settings:
- Security group name: `ecs-lab-asset-sg`
- VPC: `ecs-lab-vpc`
- Inbound rules: Allow TCP port 8080 from `ecs-lab-ui-sg`
- Outbound rules: Allow all traffic

![alt text](/images/1-prerequisites/1-security-groups/image.png)
*Figure 1: Assets Security Group configuration*

![alt text](/images/1-prerequisites/1-security-groups/image-1.png)
*Figure 2: Successfully created Assets Security Group*

---

#### Create Catalog Security Group

Navigate to [Security Groups](https://console.aws.amazon.com/ec2/home?SecurityGroups:) > Create security group

Configure the following settings:
- Security group name: `ecs-lab-catalog-sg`
- VPC: `ecs-lab-vpc`
- Inbound rules: Allow TCP port 8080 from `ecs-lab-ui-sg`
- Outbound rules: Allow all traffic

![alt text](/images/1-prerequisites/1-security-groups/image-2.png)
*Figure 3: Catalog Security Group configuration*

![alt text](/images/1-prerequisites/1-security-groups/image-3.png)
*Figure 4: Successfully created Catalog Security Group*

You have successfully created Security Groups for both the Assets and Catalog services.