---
title: "IAM Roles"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: "<b> 3. </b>"
---

{{% notice info %}}
Prerequisites: Complete the following labs before proceeding: [Fundamentals](/ecs-immersion-day/en-US/30-basic), [ECS Service Connect](/ecs-immersion-day/en-US/60-networking/ecs-service-connect)
{{% /notice %}}

#### Understanding IAM Roles in Amazon ECS

![IAM-Role](/images/3-iam-roles/ECS-Lab-Networking-IAM-Role.png)
*Figure 1. IAM Roles Diagram*

Amazon ECS uses two distinct IAM roles to manage permissions for containerized applications: the Task Execution Role and the Task IAM Role. Each role serves a specific purpose in securing your container workloads and their interactions with AWS services.

#### Task Execution Role

The Task Execution Role grants the Amazon ECS container agent permission to perform essential container management operations:

- Pulling container images from Amazon Elastic Container Registry (ECR)
- Publishing container logs to Amazon CloudWatch Logs
- Retrieving sensitive data from AWS Secrets Manager or AWS Systems Manager Parameter Store

Without a properly configured Task Execution Role, the ECS agent cannot perform these fundamental operations, potentially preventing your containers from starting successfully.

#### Task IAM Role

The Task IAM Role provides permissions directly to your application code running within the containers:

- Controls access to AWS services and resources required by your application
- Implements the principle of least privilege by limiting permissions to only what is necessary
- Maintains security isolation between different tasks and the underlying EC2 instances
- Enables granular access control through IAM policies

#### Required Trust Policy

To enable ECS tasks to assume their designated IAM roles, implement this trust policy:

```json
{
   "Version": "2012-10-17",
   "Statement": [
      {
         "Sid": "",
         "Effect": "Allow",
         "Principal": {
         "Service": "ecs-tasks.amazonaws.com"
         },
         "Action": "sts:AssumeRole"
      }
   ]
}
```

This trust policy configuration:
- Establishes the necessary trust relationship between ECS tasks and IAM roles
- Ensures proper security boundaries between different components
- Enables secure role assumption by the ECS task runtime

Review the [ECS Security Best Practices](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/security.html) documentation for comprehensive security guidance.