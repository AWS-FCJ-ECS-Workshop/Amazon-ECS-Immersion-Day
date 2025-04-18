---
title: "Creating EFS File System"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: "<b> 2. </b>"
---

#### What is AWS Elastic File System?

EFS is a file-level, fully managed, storage provided by Amazon Web Services that can be accessed by multiple EC2 instances concurrently.

#### How does EFS Work?

EFS can be created using the EC2-Instance where it can be created in specific region and distributed across multiple availability zones for the purpose of high availability and durability.

Once the EFS is created you need to set up mount targets which will provide the connectivity to your EFS file system. Following are some of the resources which you can mount on EFS.

![How does EFS Work?](/images/2-efs-volume-with-fargate/image.png)
*Figure 1. How does EFS work?*

---

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