---
title: "Creating EFS File System"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: "<b> 2. </b>"
---

#### What is AWS Elastic File System?

Amazon Elastic File System (EFS) is a fully managed, scalable file storage service that enables you to share file data across multiple Amazon EC2 instances concurrently. It provides a simple, serverless, and elastic file system that automatically grows and shrinks as you add and remove files.

#### How does EFS Work?

Amazon EFS creates and manages file systems that can be accessed simultaneously by multiple EC2 instances across different Availability Zones within a region. This distributed architecture ensures high availability and durability of your data.

To use EFS, you must create mount targets in your VPC that enable connectivity between your EC2 instances and the EFS file system. Mount targets act as access points that your applications can use to connect to the file system.

![How does EFS Work?](/images/2-efs-volume-with-fargate/image.png)
*Figure 1. How does EFS work?*

#### Create EFS File System

1. Access the [EFS Dashboard](https://console.aws.amazon.com/efs/home/file-systems) and select **Create File System**

![alt text](/images/1-prerequisites/image-2.png)
*Figure 4. EFS Dashboard interface*

2. Configure File System Settings:
   - Enter a descriptive name for your file system
   - Maintain default configuration options

![alt text](/images/1-prerequisites/image-3.png)
*Figure 5. File System Settings interface*

3. Configure Network Access:
   - Select VPC: `ecs-lab-vpc`
   - Configure mount targets in two Availability Zones
   - Select security group: `ecs-lab-efs-sg`

![alt text](/images/1-prerequisites/image-4.png)
*Figure 6. Network Access configuration*

4. Verify EFS file system creation

![alt text](/images/1-prerequisites/image-5.png)
*Figure 7. EFS File System created successfully*

#### Attach Policy to ECS IAM Role

1. Navigate to the [IAM Role console](console.aws.amazon.com/iam/home?roles) and select `retailStoreECSTaskRole`

![alt text](/images/1-prerequisites/image-6.png)
*Figure 8. IAM Role interface*

2. Select **Add permissions**

![alt text](/images/1-prerequisites/image-7.png)
*Figure 9. Add policy to IAM Role*

3. Attach required policy:
   - Search for `AmazonEFSCSIDriverPolicy`
   - Select the policy
   - Choose **Add permissions**

![alt text](/images/1-prerequisites/image-8.png)
*Figure 10. Policy attachment interface*

4. Confirm policy attachment

![alt text](/images/1-prerequisites/image-9.png)
*Figure 11. Policy attached successfully*