---
title: "Shared Responsibility Model"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: "<b> 1. </b>"
---

The security and compliance of a managed service like Amazon ECS is a shared responsibility between you and AWS. In general, AWS is responsible for security "of" the cloud, while you, the customer, are responsible for security "in" the cloud. AWS manages the security of the Amazon ECS control plane, including the infrastructure required to deliver a secure and reliable service. You, on the other hand, are primarily responsible for the topics covered in this guide, which include data, network, and runtime security, as well as logging and monitoring.

![ECS EC2](/images/1-shared-responsibility-model/image.png)
*Figure 1. AWS Shared Responsibility Model for Amazon ECS with EC2*

Regarding infrastructure security, AWS assumes more responsibility for AWS Fargate resources compared to other self-managed instances. With Fargate, AWS manages the security of the underlying cloud instance and the runtime used to execute your tasks. Additionally, Fargate automatically scales your infrastructure on your behalf.

![ECS Fargate](/images/1-shared-responsibility-model/image-1.png)
*Figure 2. AWS Shared Responsibility Model for Amazon ECS with Fargate*

For more detailed information about the shared responsibility model, please visit the [Shared Responsibility Model](https://aws.amazon.com/compliance/shared-responsibility-model) page on the AWS website.