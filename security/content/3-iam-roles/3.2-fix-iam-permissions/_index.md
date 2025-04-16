---
title: "Fix IAM Permissions"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: "<b> 3.2. </b>"
---

To resolve the issue, we need to configure an appropriate ECS Task IAM Role. Follow these steps to address the problem:

Define a policy with the necessary permissions to access DynamoDB:
```bash
    cat << EOF > carts-dynamodb-policy.json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": [
                    "dynamodb:*"
                ],
                "Resource": [
                    "arn:aws:dynamodb:$AWS_REGION:$ACCOUNT_ID:table/*"
                ]
            }
        ]
    }
    EOF
```
    

Next, use the role that will be assumed by the ECS tasks. This role must include a trust relationship policy that allows the ecs-tasks.amazonaws.com service to assume it. There is already an ECS Task Role `retailStoreEcsTaskRole` that has the following trust relationship policy defined:

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

Now attach the DynamoDB access policy we created to the ECS task role used by the Carts service:

```bash
    aws iam put-role-policy \
        --role-name retailStoreEcsTaskRole \
        --policy-name CartsDynamoPolicy \
        --policy-document file://carts-dynamodb-policy.json
```

![Attach dynamodb access policy](/images/3-iam-roles/3.2-fix-iam-permissionsimage.png)
*Figure 1. Attach DynamoDB access policy*

The next task for the Carts service that starts will pick up the new IAM permissions and enter a [Running](https://console.aws.amazon.com/ecs/v2/clusters/retail-store-ecs-cluster/services/carts/tasks) state:

![Task Running](/images/3-iam-roles/3.2-fix-iam-permissionsimage-1.png)
*Figure 2. Task running*