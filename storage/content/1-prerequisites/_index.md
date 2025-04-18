---
title: "Prerequisites"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: "<b> 1. </b>"
---

Before proceeding with the Storage section labs, we need to create a security group for EFS to allow Asset access to the EFS File System, and attach the necessary policy to the ECS IAM Role to enable EFS file system management by the ECS Cluster.

![alt text](/images/1-prerequisites/ECS-Lab-Networking-Storage-1.png)
*Figure 1. Storage architecture*

#### Create Security Group

Navigate to [Security Group Dashboard](https://console.aws.amazon.com/ec2/home?#SecurityGroups:)

In the Create Security Group interface:
- Enter a security group name `ecs-lab-efs-sg`
- Select VPC `ecs-lab-vpc`
- Add Inbound rule: Type `NFS` - Source `ecs-lab-asset-sg`

![alt text](/images/1-prerequisites/image-1.png)
*Figure 2. Create Security Group interface*

Result:

![alt text](/images/1-prerequisites/image.png)
*Figure 3. Security Group created successfully*

#### Create EFS File System

1. Navigate to [EFS Dashboard](https://console.aws.amazon.com/efs/home/file-systems) > Select Create File System

![alt text](/images/1-prerequisites/image-2.png)
*Figure 4. EFS Dashboard interface*

2. In File System Settings > Enter name, keep default options

![alt text](/images/1-prerequisites/image-3.png)
*Figure 5. File System Settings interface*

3. In Network Access, select VPC `ecs-lab-vpc`, configure mount targets in 2 AZs with `ecs-lab-efs-sg`

![alt text](/images/1-prerequisites/image-4.png)
*Figure 6. Network Access configuration*

4. EFS file system creation complete

![alt text](/images/1-prerequisites/image-5.png)
*Figure 7. EFS File System created successfully*

#### Attach Policy to ECS IAM Role

1. Navigate to [IAM Role interface](console.aws.amazon.com/iam/home?roles) and select `retailStoreECSTaskRole`

![alt text](/images/1-prerequisites/image-6.png)
*Figure 8. IAM Role interface*

2. Add policy to IAM Role

![alt text](/images/1-prerequisites/image-7.png)
*Figure 9. Add policy to IAM Role*

3. Search and select `AmazonEFSCSIDriverPolicy` > Add Permissions

![alt text](/images/1-prerequisites/image-8.png)
*Figure 10. Policy attachment interface*

4. Policy attached successfully:

![alt text](/images/1-prerequisites/image-9.png)
*Figure 11. Policy attached successfully*