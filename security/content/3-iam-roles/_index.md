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

Amazon ECS tasks can be assigned IAM roles that grant specific permissions to containers running within the task. These roles enable secure interactions between your containerized applications and other AWS services.

#### Task Execution Role

The task execution role enables the Amazon ECS container agent to perform actions on your behalf:

- Pull container images from Amazon ECR
- Write container logs to Amazon CloudWatch Logs
- Retrieve sensitive information from AWS Secrets Manager (e.g., image pull secrets)

#### Task IAM Role

Task IAM roles provide permissions directly to your application code running inside containers:

- Follows the principle of least privilege
- Enables fine-grained access control to AWS services
- Provides isolation from EC2 instance roles

#### Required Trust Policy

When creating task IAM roles, implement the following trust policy to ensure proper role assumption:

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

This trust policy ensures:
- Tasks can assume their designated IAM roles
- Role isolation between tasks and EC2 instances
- Proper security boundaries

For additional security guidance, consult the [ECS Security Best Practices](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/security.html) documentation.