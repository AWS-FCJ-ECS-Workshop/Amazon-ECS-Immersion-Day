---
title: "Clean Resources"
date: "`r Sys.Date()`"
weight: 5
chapter: false
pre: "<b> 5. </b>"
---

{{% notice info %}}
Important: This Clean Up Resources section will only delete the services created within this section.
To remove all resources, you must delete them sequentially starting from the [Fundamentals](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/fundamentals/) section.
{{% /notice %}}

This guide walks you through cleaning up all resources created during the workshop to avoid ongoing charges.

#### Security Groups
1. Delete the Cart Security Group (`ecs-lab-cart-sg`)
![Delete Cart Security Group](/images/5-clean-resources/image.png)

2. Delete Asset and Catalog Security Groups
![Delete Asset Security Group](/images/5-clean-resources/image-4.png)
![Delete Catalog Security Group](/images/5-clean-resources/image-5.png)

#### Database Resources
1. Delete DynamoDB tables
![Delete DynamoDB](/images/5-clean-resources/image-1.png)

2. Delete MySQL Aurora Database Setup
![Delete Aurora Database](/images/5-clean-resources/image-8.png)

#### Service Resources
1. Delete Cart Services
![Delete Cart Services](/images/5-clean-resources/image-2.png)

2. Delete Assets & Catalog Services
![Delete Assets and Catalog](/images/5-clean-resources/image-13.png)

#### Network Resources
1. Delete Cloud Map Namespaces
![Delete Cloud Map](/images/5-clean-resources/image-6.png)

2. Delete TLS-enabled Target Group for UI
![Delete Target Group](/images/5-clean-resources/image-12.png)

#### Security Resources
1. Disable Amazon GuardDuty
![Disable GuardDuty](/images/5-clean-resources/image-3.png)

2. Delete Catalog IAM Role Configuration
![Delete IAM Role](/images/5-clean-resources/image-7.png)

3. Delete AWS Private Certificate Authority
![Delete Private CA](/images/5-clean-resources/image-10.png)

4. Delete AWS Certificate Manager certificates
![Delete ACM Certificates](/images/5-clean-resources/image-11.png)

#### Parameter Store Resources
Delete Secret Manager variables by running:
```bash
aws ssm delete-parameter \
--name /retail-store-ecs/catalog/db-endpoint
```
![Delete Parameters](/images/5-clean-resources/image-9.png)

Verify all resources are properly deleted to avoid unexpected charges on your AWS account.