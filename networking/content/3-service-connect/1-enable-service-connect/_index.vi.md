---
title: "Kích hoạt Service Connect"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: "<b> 3.1. </b>"
---

Phần này hướng dẫn cách kích hoạt Amazon ECS Service Connect bằng cách triển khai thêm hai microservice tương tác với service UI:

![AWS ECS service Connect Architecture](https://static.us-east-1.prod.workshops.aws/public/fe1738fc-3d5c-4d22-bac3-0be10a3ad36f/static/images/60-networking/service-connect-architecture.png)
*Hình 1. Kiến trúc AWS ECS Service Connect*

#### Triển khai service Assets

Đầu tiên, tạo task definition ECS cho service Assets:

```bash
cat << EOF > retail-store-ecs-asset-taskdef.json
{
    "family": "retail-store-ecs-assets",
    "executionRoleArn": "arn:aws:iam::${ACCOUNT_ID}:role/retailStoreEcsTaskExecutionRole",
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
            "image": "public.ecr.aws/aws-containers/retail-store-sample-assets:0.7.0",
            "cpu": 0,
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
            "healthCheck": {
                "command": [
                    "CMD-SHELL",
                    "curl -f http://localhost:8080/health.html || exit 1"
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
                    "awslogs-stream-prefix": "assets-service"
                }
            }
        }
    ]
}
EOF

aws ecs register-task-definition --cli-input-json file://retail-store-ecs-asset-taskdef.json
```

Tiếp theo, tạo service ECS Assets với Service Connect được kích hoạt:

{{% notice warning %}} Đảm bảo `$ASSET_SG_ID` đã được gán giá trị. Nếu không có nó, service sẽ được tạo mà không có security group. {{% /notice %}}

```bash
aws ecs create-service \
    --cluster retail-store-ecs-cluster \
    --service-name assets \
    --task-definition retail-store-ecs-assets \
    --desired-count 1 \
    --launch-type FARGATE \
    --network-configuration "awsvpcConfiguration={subnets=[${PRIVATE_SUBNET1}, ${PRIVATE_SUBNET2}], securityGroups=[$ASSET_SG_ID],assignPublicIp=DISABLED}" \
    --service-connect-configuration '{
        "enabled": true,
        "namespace": "retailstore.local",
        "services": [
            {
                "portName": "application",
                "discoveryName": "assets",
                "clientAliases": [
                    {
                        "port": 80,
                        "dnsName": "assets"
                    }
                ]
            }
        ]
    }'
```

Cấu hình Service Connect cho phép service discovery và kết nối, định nghĩa namespace chung cho giao tiếp giữa các service, và cấu hình alias cùng port mapping cho service.

#### Triển khai service Catalog

Tạo task definition cho service Catalog:

```bash
cat << EOF > retail-store-ecs-catalog-taskdef.json
{
    "family": "retail-store-ecs-catalog",
    "executionRoleArn": "arn:aws:iam::${ACCOUNT_ID}:role/cataologEcsTaskExecutionRole",
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
            "image": "public.ecr.aws/aws-containers/retail-store-sample-catalog:0.7.0",
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
                    "name": "DB_NAME",
                    "value": "catalog"
                }
            ],
            "secrets": [
                {
                    "name": "DB_ENDPOINT",
                    "valueFrom": "arn:aws:ssm:${AWS_REGION}:${ACCOUNT_ID}:parameter/retail-store-ecs/catalog/db-endpoint"
                },
                {
                    "name": "DB_PASSWORD",
                    "valueFrom": "arn:aws:secretsmanager:${AWS_REGION}:${ACCOUNT_ID}:secret:retail-store-ecs-catalog-db:password::"
                },
                {
                    "name": "DB_USER",
                    "valueFrom": "arn:aws:secretsmanager:${AWS_REGION}:${ACCOUNT_ID}:secret:retail-store-ecs-catalog-db:username::"
                }
            ],
            "healthCheck": {
                "command": [
                    "CMD-SHELL",
                    "curl -f http://localhost:8080/health || exit 1"
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
                    "awslogs-stream-prefix": "catalog-service"
                }
            }
        }
    ]
}
EOF

aws ecs register-task-definition --cli-input-json file://retail-store-ecs-catalog-taskdef.json
```

Tạo service ECS Catalog với Service Connect được kích hoạt:

{{% notice warning %}} Đảm bảo `$CATALOG_SG_ID` đã được gán giá trị. Nếu không có nó, service sẽ được tạo mà không có security group. {{% /notice %}}

```bash
aws ecs create-service \
    --cluster retail-store-ecs-cluster \
    --service-name catalog \
    --task-definition retail-store-ecs-catalog \
    --desired-count 1 \
    --launch-type FARGATE \
    --network-configuration "awsvpcConfiguration={subnets=[${PRIVATE_SUBNET1}, ${PRIVATE_SUBNET2}], securityGroups=[$CATALOG_SG_ID],assignPublicIp=DISABLED}" \
    --service-connect-configuration '{
        "enabled": true,
        "namespace": "retailstore.local",
        "services": [
            {
                "portName": "application",
                "discoveryName": "catalog",
                "clientAliases": [
                    {
                        "port": 80,
                        "dnsName": "catalog"
                    }
                ]
            }
        ]
    }'
```

#### Cập nhật service UI

Đợi các service mới triển khai xong (khoảng 2 phút):
```bash
aws ecs wait services-stable --cluster retail-store-ecs-cluster --services catalog
aws ecs wait services-stable --cluster retail-store-ecs-cluster --services assets
```

Cập nhật task definition UI để thêm các biến môi trường kết nối tới service Catalog và Assets:

```bash
cat << EOF > retail-store-ecs-ui-connect-taskdef.json
{
    "family": "retail-store-ecs-ui",
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
            "image": "public.ecr.aws/aws-containers/retail-store-sample-ui:0.7.0",
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
            "linuxParameters": {
                "initProcessEnabled": true
            },
            "environment": [
                {
                    "name": "ENDPOINTS_CATALOG",
                    "value": "http://catalog"
                },
                {
                    "name": "ENDPOINTS_ASSETS",
                    "value": "http://assets"
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
                    "awslogs-stream-prefix": "ui-service"
                }
            }
        }
    ]
}
EOF

aws ecs register-task-definition --cli-input-json file://retail-store-ecs-ui-connect-taskdef.json
```

Giờ cập nhật service UI với task definition mới và kích hoạt Service Connect:

```bash
aws ecs update-service \
    --cluster retail-store-ecs-cluster \
    --service ui \
    --task-definition retail-store-ecs-ui \
    --force-new-deployment \
    --service-connect-configuration '{
        "enabled": true,
        "namespace": "retailstore.local",
        "services": [
            {
                "portName": "application",
                "discoveryName": "ui",
                "clientAliases": [
                    {
                        "port": 80,
                        "dnsName": "ui"
                    }
                ]
            }
        ]
    }'

echo "Đang đợi service ổn định..."

aws ecs wait services-stable --cluster retail-store-ecs-cluster --services ui
```

#### Truy cập Ứng dụng Web

Để xem ứng dụng đã được nâng cấp với hình ảnh sản phẩm, lấy DNS name của ALB:

```bash
export RETAIL_ALB=$(aws elbv2 describe-load-balancers --name retail-store-ecs-ui \
 --query 'LoadBalancers[0].DNSName' --output text)

echo http://${RETAIL_ALB}
```

Truy cập URL ứng dụng trong trình duyệt web để xem giao diện hoàn chỉnh với hình ảnh sản phẩm:

![alt text](/images/3-service-connect/1-enable-service-connect/image.png)
*Hình 2. Giao diện Ứng dụng Web*