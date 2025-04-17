---
title: "Gắn IAM Policy cần thiết"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: "<b> 4.2. </b>"
---

Agent bảo mật của Amazon GuardDuty hoạt động như một sidecar container bên cạnh các container ứng dụng của bạn. Agent được phân phối dưới dạng container image được lưu trữ trong Amazon Elastic Container Registry (Amazon ECR).

Để cho phép agent GuardDuty hoạt động đúng cách, nó cần các quyền cụ thể để kéo container image từ Amazon ECR. Những quyền này phải được cấp thông qua [ECS task execution IAM role](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_execution_IAM_role.html).

IAM policy sau đây cấp các quyền cần thiết:

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

Lưu ý: Nếu bạn đang sử dụng policy được quản lý bởi AWS `AmazonECSTaskExecutionRolePolicy`, các quyền này đã được bao gồm.

Để xác minh rằng `AmazonECSTaskExecutionRolePolicy` đã được gắn vào ECS task execution role của bạn, hãy thực thi lệnh AWS CLI này:

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
*Hình 1. Chi tiết `AmazonECSTaskExecutionRolePolicy`*