---
title: "Clean Resources"
date: "`r Sys.Date()`"
weight: 4
chapter: false
pre: "<b> 4. </b>"
---

{{% notice info %}}
Important: This Clean Up Resources section will only delete the services created within this section.
To remove all resources, you must delete them sequentially starting from the [Fundamentals](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/fundamentals/) section.
{{% /notice %}}

#### Delete EFS Security Group

Navigate to the Security Groups Dashboard:
1. Select `ecs-lab-efs-sg`
2. Click **Delete**

![alt text](/images/4-clean-resources/image.png)
*Figure 1: Deleting the EFS Security Group*

#### Delete EFS

Navigate to the EFS Dashboard:
1. Select `ecs-lab-assets-efs`
2. Click **Delete**

![alt text](/images/4-clean-resources/image-1.png)
*Figure 2: Deleting the Elastic File System*