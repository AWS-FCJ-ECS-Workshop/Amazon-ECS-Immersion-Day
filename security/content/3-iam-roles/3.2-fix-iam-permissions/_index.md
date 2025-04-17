---
title: "Fix IAM Permissions"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: "<b> 3.2. </b>"
---

Configure the appropriate ECS Task IAM Role by following these steps to grant the Carts service access to DynamoDB:

1. Create a policy document that defines the required DynamoDB permissions:
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

The existing ECS Task Role `retailStoreEcsTaskRole` already includes the required trust relationship policy that enables the ecs-tasks.amazonaws.com service to assume it:

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

2. Attach the DynamoDB access policy to the ECS task role:

```bash
aws iam put-role-policy \
    --role-name retailStoreEcsTaskRole \
    --policy-name CartsDynamoPolicy \
    --policy-document file://carts-dynamodb-policy.json
```

![Attach dynamodb access policy](/images/3-iam-roles/3.2-fix-iam-permissions/image.png)
*Figure 1. Attach DynamoDB access policy*

Once configured, new Carts service tasks will automatically use the updated IAM permissions and transition to a [Running](https://console.aws.amazon.com/ecs/v2/clusters/retail-store-ecs-cluster/services/carts/tasks) state:

![Task Running](/images/3-iam-roles/3.2-fix-iam-permissions/image-1.png)
*Figure 2. Task running*