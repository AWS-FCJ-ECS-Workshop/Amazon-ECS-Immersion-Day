---
title: "Shared Responsibility Model"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: "<b> 1. </b>"
---

The AWS Shared Responsibility Model defines security responsibilities between AWS and customers using Amazon Elastic Container Service (Amazon ECS). This model distinguishes between:

* AWS responsibilities - Security "of" the cloud
* Customer responsibilities - Security "in" the cloud

AWS maintains complete control over the security of the Amazon ECS control plane and its underlying infrastructure to ensure service reliability and security. As a customer, you are responsible for:

* Data security
* Network security
* Runtime security
* Logging and monitoring
* Access management
* Resource configuration

![ECS EC2](/images/1-shared-responsibility-model/image.png)
*Figure 1. AWS Shared Responsibility Model for Amazon ECS with EC2*

When using AWS Fargate with Amazon ECS, AWS assumes additional security responsibilities compared to EC2 instances:

* Management of underlying cloud infrastructure
* Runtime environment security for task execution
* Automatic infrastructure scaling
* Operating system maintenance and patching

![ECS Fargate](/images/1-shared-responsibility-model/image-1.png)
*Figure 2. AWS Shared Responsibility Model for Amazon ECS with Fargate*

Learn more about security responsibilities by visiting the official [AWS Shared Responsibility Model](https://aws.amazon.com/compliance/shared-responsibility-model) documentation.