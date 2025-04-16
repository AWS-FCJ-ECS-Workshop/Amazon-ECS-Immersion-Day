---
title: "Create Cart Service"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: "<b> 3.1. </b>"
---

Trong phần này, chúng ta sẽ triển khai microservice `Carts`. Service này cung cấp API chuyên dụng để quản lý giỏ hàng của người dùng, với việc lưu trữ dữ liệu được xử lý bởi [Amazon DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html).

## Tạo ECS Task Definition

Đầu tiên, tạo ECS task definition cho service Carts:

```bash
cat << EOF > retail-store-ecs-carts-taskdef.json
{
   "family": "retail-store-ecs-carts",
   "executionRoleArn": "arn:aws:iam::${ACCOUNT_ID}:role/retailStoreEcsTaskExecutionRole",
   "taskRoleArn": "arn:aws:iam::${ACCOUNT_ID}:role/retailStoreEcsTaskRole",
   "networkMode": "awsvpc",
   "requiresCompatibilities": [
      "FARGATE"
   ],
   "cpu": "1024",
   "memory": "2048",
   "runtimePlatform": {
      "cpuArchitecture": "X86_64",
      "operatingSystemFamily": "LINUX"
   },
   "containerDefinitions": [
      {
         "name": "application",
         "image": "public.ecr.aws/aws-containers/retail-store-sample-cart:0.8.0",
         "portMappings": [
            {
               "name": "application", 
               "containerPort": 8080,
               "hostPort": 8080,
               "protocol": "tcp",
               "appProtocol": "http"
            }
         ],
         "essential": true,
         "environment": [
            {
               "name": "CARTS_DYNAMODB_TABLENAME",
               "value": "retail-store-ecs-carts"
            },
            {
               "name": "SPRING_PROFILES_ACTIVE",
               "value": "dynamodb"
            }
         ],
         "healthCheck": {
            "command": [
               "CMD-SHELL",
               "curl -f http://localhost:8080/actuator/health || exit 1"
            ],
            "interval": 10,
            "timeout": 5,
            "retries": 3,
            "startPeriod": 60
         },
         "logConfiguration": {
            "logDriver": "awslogs",
            "options": {
               "awslogs-group": "retail-store-ecs-tasks",
               "awslogs-region": "$AWS_REGION",
               "awslogs-stream-prefix": "carts-service"
            }
         }
      }
   ]
}
EOF

aws ecs register-task-definition --cli-input-json file://retail-store-ecs-carts-taskdef.json
```

Task definition bao gồm tham số `taskRoleArn` tham chiếu đến một IAM role hiện có. Chúng ta sẽ xem xét quyền của role này sau.

## Triển khai Carts Service

Tạo ECS service bằng lệnh sau:

```bash
aws ecs create-service \
  --cluster retail-store-ecs-cluster \
  --service-name carts \
  --task-definition retail-store-ecs-carts \
  --desired-count 1 \
  --launch-type FARGATE \
  --network-configuration "awsvpcConfiguration={subnets=[${PRIVATE_SUBNET1}, ${PRIVATE_SUBNET2}], securityGroups=[$CART_SG_ID],assignPublicIp=DISABLED}" \
  --service-connect-configuration '{
     "enabled": true,
     "namespace": "retailstore.local",
     "services": [
           {
              "portName": "application",
              "discoveryName": "carts",
              "clientAliases": [
                 {
                       "port": 80,
                       "dnsName": "carts"
                 }
              ]
           }
     ]
  }'
```

## Giám sát và Xử lý sự cố Triển khai

1. Truy cập vào [ECS console](https://console.aws.amazon.com/ecs/v2/clusters/retail-store-ecs-cluster/services/carts/tasks) để theo dõi việc triển khai.

2. Nếu trạng thái triển khai vẫn là **In progress**, lọc các task **Stopped** để điều tra lỗi.

![Failed tasks console](image.png)

3. Xem logs container trong tab **Logs**:

![Logs from failed task](image-1.png)

Bạn có thể gặp thông báo lỗi tương tự:

```bash
User: arn:aws:sts::XXXXXXXXXXXX:assumed-role/retailStoreEcsTaskRole/172891fb75674ba998f05e9fe855fc74
is not authorized to perform: dynamodb:Query on resource: arn:aws:dynamodb:us-west-2:XXXXXXXXXXXX:table/retail-store-ecs-carts/index/idx_global_customerId
because no identity-based policy allows the dynamodb:Query action
```

Lỗi này cho thấy không đủ quyền IAM để truy cập bảng DynamoDB. Kiểm tra quyền hiện tại trong [IAM console](https://console.aws.amazon.com/iam/home#/roles/details/retailStoreEcsTaskRole?section=permissions).