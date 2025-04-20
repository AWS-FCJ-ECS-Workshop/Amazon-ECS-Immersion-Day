---
title : "Getting Started"
date :  "`r Sys.Date()`" 
weight : 1
chapter : false
pre: "<b> 1. </b>"
---

#### Sample Application

This workshop uses a containerized sample retail application for hands-on practice. The application simulates a basic e-commerce store where customers can browse products, add items to their cart, and complete purchases through a checkout process.

![alt text](/images/1-getting-started/image.png)

The application consists of the following components and dependencies:

![Service Components](/images/1-getting-started/image-1.png)

**Sample Application Architecture:**

| Component | Description |
|-----------|-------------|
| UI | Frontend interface that aggregates API calls to backend services |
| Catalog | API providing product listings and details |
| Cart | API managing customer shopping carts |
| Checkout | API orchestrating the checkout process |
| Orders | API processing and receiving customer orders |
| Static Assets | Serves static files such as product images |

The complete source code is available on [GitHub](https://github.com/aws-containers/retail-store-sample-app).

#### Containerizing Workloads

Before deploying workloads to Amazon ECS, they must be packaged as container images and pushed to a container registry in subsequent steps.

The following table lists the source ECR repository paths and corresponding source Dockerfiles:

| Component | ECR Repository | Dockerfile |
|-----------|---------------|------------|
| UI | [Link](https://gallery.ecr.aws/aws-containers/retail-store-sample-ui) | [Dockerfile](https://github.com/aws-containers/retail-store-sample-app/blob/0.8.5/src/ui/Dockerfile) |
| Catalog | [Link](https://gallery.ecr.aws/aws-containers/retail-store-sample-catalog) | [Dockerfile](https://github.com/aws-containers/retail-store-sample-app/blob/0.8.5/src/catalog/Dockerfile) |
| Shopping Cart | [Link](https://gallery.ecr.aws/aws-containers/retail-store-sample-cart) | [Dockerfile](https://github.com/aws-containers/retail-store-sample-app/blob/0.8.5/src/cart/Dockerfile) |
| Checkout | [Link](https://gallery.ecr.aws/aws-containers/retail-store-sample-checkout) | [Dockerfile](https://github.com/aws-containers/retail-store-sample-app/blob/0.8.5/src/checkout/Dockerfile) |
| Orders | [Link](https://gallery.ecr.aws/aws-containers/retail-store-sample-orders) | [Dockerfile](https://github.com/aws-containers/retail-store-sample-app/blob/0.8.5/src/orders/Dockerfile) |
| Assets | [Link](https://gallery.ecr.aws/aws-containers/retail-store-sample-assets) | [Dockerfile](https://github.com/aws-containers/retail-store-sample-app/blob/0.8.5/src/assets/Dockerfile) |

#### Expected Outcomes

The **Fundamentals** section will help you develop a solid understanding of Amazon ECS and AWS Fargate core concepts, including clusters, task definitions, services, networking, and deployment architecture. Upon completion, you will be able to confidently deploy containerized workloads on AWS using ECS and Fargate.

#### Pricing Overview

{{% notice info %}}
⚠️ Review the pricing structure and cost factors carefully before deployment to avoid unexpected charges!
{{% /notice %}}

This workshop utilizes multiple AWS services including VPC networking, ECS (Fargate launch type), and Application Load Balancer (ALB). The cost estimates below are based on the **Asia Pacific (Sydney)** region and assume typical usage patterns for a demo or development environment.

You can view and customize this estimate using the [AWS Pricing Calculator](https://calculator.aws/#/estimate?id=850c71ae0c1cbf130c921383ceb3c5907f83e46c).

| Service Name | Description | Monthly Cost (USD) | 12-Month Cost (USD) |
|--------------|-------------|-------------------|-------------------|
| **AWS Fargate** | Container task execution | $1.80 | $21.60 |
| **Application Load Balancer** | Traffic distribution to ECS tasks | $33.00 | $396.00 |
| **Data Transfer** | Internet & inter-service traffic | $0.00 | $0.00 |
| **NAT Gateway** | Enables outbound internet access | $43.13 | $517.56 |
| **Gateway Load Balancer** | Advanced traffic filtering | $34.10 | $409.20 |
| **Total** | | **$139.04** | **$1,668.48** |