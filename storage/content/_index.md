---
title: "Prerequisites"
date: "`r Sys.Date()`"
chapter: false
---


# Prerequisites

Complete these chapters before proceeding:

*   [Fundamentals](/ecs-immersion-day/en-US/30-basic)
*   [ECS Service Connect](/ecs-immersion-day/en-US/60-networking/ecs-service-connect)

# Storage Options for ECS Tasks

Amazon ECS provides multiple volume types to support diverse storage requirements:

*   Amazon EBS
*   Amazon EFS
*   Amazon FSx for Windows File Server
*   Docker Volumes
*   Bind mounts

For comprehensive details on all volume options, consult the [Amazon ECS storage documentation](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/using_data_volumes.html).

Key storage solutions commonly used with ECS:

Volume | Supported Launch Type | Persistence | Description | Use Cases
-------|---------------------|-------------|-------------|------------
Amazon EBS | Fargate, Amazon EC2 | Persistent for standalone tasks; Ephemeral for service tasks | Cost-effective, durable block storage with high performance | Transactional workloads, log processing, ETL operations
Amazon EFS | Fargate, Amazon EC2 | Persistent | Fully managed, scalable file system | Data analytics, media processing, content management, web serving

# Current Architecture Limitations

Your existing Assets service deployment has two main limitations:

1. Single task deployment creates a potential point of failure
2. Product image updates require full application redeployment

# Proposed Improvements

To address these limitations, we will:

1. Scale the Assets service to multiple tasks for high availability
2. Implement Amazon EFS as shared storage for product images

Benefits of Amazon EFS integration:

* Enables multiple tasks to access shared image files simultaneously
* Simplifies image content updates without application redeployment
* Provides persistent storage across task instances

The enhanced architecture will look like this:

![Sample application ECS with EFS architecture](https://static.us-east-1.prod.workshops.aws/public/fe1738fc-3d5c-4d22-bac3-0be10a3ad36f/static/images/90-storage/retail-ui-assets-efs-architecture.png)