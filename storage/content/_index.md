---
title: "Storage"
date: "`r Sys.Date()`"
chapter: false
---

{{% notice info %}}
Complete these prerequisite chapters before starting this lab: [Fundamentals](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/fundamentals/), [ECS Service Connect](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/networking/ecs-service-connect)
{{% /notice %}}

Amazon ECS supports multiple volume types for container tasks:

*   Amazon EBS
*   Amazon EFS
*   Amazon FSx for Windows File Server
*   Docker Volumes
*   Bind mounts

For comprehensive details on volume options, consult the [Amazon ECS storage documentation](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/using_data_volumes.html). The following table compares the most commonly used volume types:

| Volume Type  | Launch Type Support        | Data Persistence                                                            | Key Features                               | Common Applications                                |
|--------------|---------------------------|----------------------------------------------------------------------------|--------------------------------------------|-------------------------------------------------|
| Amazon EBS   | Fargate, Amazon EC2       | Persistent for standalone tasks; Ephemeral for service-managed tasks        | Cost-effective, durable, high-performance   | Transactional workloads, log processing, ETL jobs |
| Amazon EFS   | Fargate, Amazon EC2       | Fully persistent                                                           | Scalable, shared file system                | Data analytics, media processing, web content     |

#### Current State and Challenges

Your Assets service currently operates with a single task, creating a potential availability risk. Additionally, updating product images requires redeploying the entire application, which is inefficient and time-consuming.

#### Proposed Improvements

To enhance the Assets service reliability and maintainability:

*   Scale to multiple tasks for high availability
*   Implement Amazon EFS for shared storage of product images

#### Benefits of Amazon EFS Integration:

*   Enables concurrent file access across multiple tasks
*   Simplifies image management without application redeployment
*   Provides persistent storage across container instances

The improved architecture will look like this:

![Sample application ECS with EFS architecture](/images/image.png)
*Figure 1. Sample application ECS with EFS architecture*