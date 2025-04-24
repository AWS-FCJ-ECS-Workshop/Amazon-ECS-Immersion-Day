---
title: "Clean resources"
date: "`r Sys.Date()`"
weight: 4
chapter: false
pre: "<b> 4. </b>"
---

{{% notice info %}}
Note: To proceed with subsequent chapters, you do not need to delete the resources created in this Fundamentals section.
{{% /notice %}}

2.1. Delete IAM Roles

Delete the following IAM roles:
- `retailStoreECSTaskExecutionRole`
- `retailStoreEcsTaskRole`

![alt text](/images/4-clean-resources/image.png)

2.2. Delete Network Resources

Remove all Virtual Private Cloud (VPC) configurations and associated networking components.

![alt text](/images/4-clean-resources/image-1.png)

2.3. Remove Security Groups

Delete the following security groups:
- `ecs-lab-public-sg`
- `ecs-lab-private-sg`
- `ecs-lab-ui-sg`

![alt text](/images/4-clean-resources/image-2.png)

2.4. Delete Load Balancer

Remove the Application Load Balancer and its associated target groups.

![alt text](/images/4-clean-resources/image-3.png)

2.5. Clean up CloudWatch Resources

Delete all CloudWatch logs, metrics, and alarms created during the lab.

![alt text](/images/4-clean-resources/image-4.png)

2.6. Clean up Lab Environment

1. Terminate all EC2 instances created for this lab
![alt text](/images/4-clean-resources/image-5.png)

2. Delete the `ecs-lab-admin-ec2` instance
![alt text](/images/4-clean-resources/image-6.png)

3. Remove any associated key pairs if no longer needed

2.7. Clean up Source Code Resources

Remove all code repositories and associated resources.

![alt text](/images/4-clean-resources/image-7.png)

2.8. Delete UI Service

Navigate to the Amazon ECS console and select the UI service in the "Services" tab. Click "Delete" to initiate the deletion process.

![alt text](/images/4-clean-resources/image-8.png)