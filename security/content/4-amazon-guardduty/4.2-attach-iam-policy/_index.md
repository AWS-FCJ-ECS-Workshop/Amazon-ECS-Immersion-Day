---
title: "Attach necessary IAM Policy"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: "<b> 4.2. </b>"
---

The GuardDuty agent is packaged as a container image hosted in Amazon Elastic Container Registry (Amazon ECR). When the GuardDuty security agent is enabled, it attaches itself to each container as a sidecar.

For the GuardDuty agent to pull the container image from Amazon ECR, it needs permission to access the service. This permission is granted by attaching an IAM policy to the [ECS task execution IAM role](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_execution_IAM_role.html)  that allows the `ecr:GetAuthorizationToken`, `ecr:BatchCheckLayerAvailability`, `ecr:GetDownloadUrlForLayer`, and `ecr:BatchGetImage` actions.

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

The required permissions are already included if you are using the `AmazonECSTaskExecutionRolePolicy` managed IAM policy.

To verify if the `AmazonECSTaskExecutionRolePolicy` is attached to the ECS task execution role we are using, run the following CLI command:

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
---
![alt text](/images/4-amazon-guardduty/4.2-attach-iam-policy/image.png)
*Figure 1. `AmazonECSTaskExecutionRolePolicy` detail*