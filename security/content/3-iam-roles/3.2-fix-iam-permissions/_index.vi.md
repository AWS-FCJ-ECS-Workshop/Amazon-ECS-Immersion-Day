---
title: "Sửa quyền IAM"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: "<b> 3.2. </b>"
---

Cấu hình ECS Task IAM Role phù hợp bằng cách làm theo các bước sau để cấp quyền truy cập DynamoDB cho service Carts:

1. Tạo tài liệu policy xác định các quyền DynamoDB cần thiết:
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

ECS Task Role `retailStoreEcsTaskRole` hiện tại đã bao gồm policy trust relationship cần thiết cho phép service ecs-tasks.amazonaws.com đảm nhận nó:

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

2. Gắn policy truy cập DynamoDB vào ECS task role:

```bash
aws iam put-role-policy \
    --role-name retailStoreEcsTaskRole \
    --policy-name CartsDynamoPolicy \
    --policy-document file://carts-dynamodb-policy.json
```

![Gắn policy truy cập dynamodb](/images/3-iam-roles/3.2-fix-iam-permissions/image.png)
*Hình 1. Gắn policy truy cập DynamoDB*

Sau khi cấu hình, các task mới của service Carts sẽ tự động sử dụng quyền IAM đã cập nhật và chuyển sang trạng thái [Running](https://console.aws.amazon.com/ecs/v2/clusters/retail-store-ecs-cluster/services/carts/tasks):

![Task đang chạy](/images/3-iam-roles/3.2-fix-iam-permissions/image-1.png)
*Hình 2. Task đang chạy*