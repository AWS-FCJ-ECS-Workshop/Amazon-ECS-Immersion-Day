---
title: "Attach Necessary IAM Policy"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: "<b> 4.2. </b>"
---

Amazon GuardDuty's security agent operates as a sidecar container alongside your application containers. The agent is distributed as a container image stored in Amazon Elastic Container Registry (Amazon ECR).

To enable the GuardDuty agent to function properly, it requires specific permissions to pull its container image from Amazon ECR. These permissions must be granted through the [ECS task execution IAM role](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_execution_IAM_role.html).

The following IAM policy grants the necessary permissions:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "ecr:GetAuthorizationToken",
                "ecr:BatchCheckLayerAvailability",
                "ecr:GetDownloadUrlForLayer",
                "ecr:BatchGetImage"
            ],
            "Resource": "*",
            "Effect": "Allow"
        }
    ]
}
```

Note: If you are using the AWS-managed policy `AmazonECSTaskExecutionRolePolicy`, these permissions are already included.

To verify that the `AmazonECSTaskExecutionRolePolicy` is attached to your ECS task execution role, execute this AWS CLI command:

```bash
aws iam list-attached-role-policies --role-name retailStoreEcsTaskExecutionRole

{
    "AttachedPolicies": [
        {
            "PolicyName": "AmazonECSTaskExecutionRolePolicy",
            "PolicyArn": "arn:aws:iam::aws:policy/service-role/AmazonECSTaskExecutionRolePolicy"
        }
    ]
}
```

![alt text](/images/4-amazon-guardduty/4.2-attach-iam-policy/image.png)
*Figure 1. `AmazonECSTaskExecutionRolePolicy` detail*