---
title: "Amazon Elastic Container Service (ECS)"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: "<b> 3. </b>"
---

### Amazon ECS Core Components

![alt text](/images/3-fundamentals/image.png)

Amazon ECS (Elastic Container Service) consists of 4 core components:

#### Cluster
- Is a logical group of services and tasks
- Functions as an independent container runtime environment
- Can run on EC2 instances or serverless with Fargate
- Allows resource management and compute capacity allocation

#### Service
- Maintains and manages a group of identical tasks
- Ensures desired number of tasks is always maintained
- Integrates with Application Load Balancer for traffic distribution
- Supports auto-scaling and self-healing

{{< youtube fXiUlXy5kRA >}}

#### Task
- Represents one or more containers performing a specific application function
- Is the smallest deployable unit in ECS
- Runs independently or as part of a service
- Is defined by a task definition

#### Task Definition
- Blueprint for running containers in ECS
- Specifies:
  - Container images
  - CPU and Memory requirements
  - Ports and volumes mapping
  - Networking configuration
  - IAM roles
  - Environment variables
  - Logging configuration

{{< youtube 5uJUmGWjRZY >}}

Understanding these components is crucial for effectively deploying and operating containerized applications on Amazon ECS. Each component plays an essential role in the overall architecture of the container system.