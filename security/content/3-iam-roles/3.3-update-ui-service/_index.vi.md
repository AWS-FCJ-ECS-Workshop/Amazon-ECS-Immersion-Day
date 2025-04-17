---
title: "Cập nhật dịch vụ UI"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: "<b> 3.3. </b>"
---

Phần này đề cập đến việc cập nhật dịch vụ `UI` để tích hợp với dịch vụ `Carts`. Việc tích hợp cho phép ứng dụng Retail Store Sample lưu trữ các mặt hàng trong giỏ hàng của khách hàng trong [Amazon DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html) thông qua dịch vụ `Carts`.

Để kết nối dịch vụ `UI` với dịch vụ `Carts`, thêm biến môi trường sau vào task definition của UI:

```json
    "environment": [
        {
            "name": "ENDPOINTS_CARTS",
            "value": "http://carts"
        }
    ]
```

Cập nhật **UI task definition** bằng cách thực thi các lệnh sau:

```bash
    cat << EOF > retail-store-ecs-ui-updatedforcart-taskdef.json
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
                    },
                    {
                         "name": "ENDPOINTS_CARTS",
                         "value": "http://carts"
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
    
    aws ecs register-task-definition --cli-input-json file://retail-store-ecs-ui-updatedforcart-taskdef.json
```

Cập nhật dịch vụ ECS với task definition mới nhất (quá trình này mất khoảng 6 phút):

```bash
    aws ecs update-service \
        --cluster retail-store-ecs-cluster \
        --service ui \
        --task-definition retail-store-ecs-ui
    
    echo "Waiting for service to stabilize..."
    
    aws ecs wait services-stable --cluster retail-store-ecs-cluster --services ui
```

![Triển khai thành công](/images/3-iam-roles/3.3-update-ui-service/image.png)
*Hình 1. Xác nhận triển khai thành công*

Sau khi triển khai hoàn tất thành công, hãy chuyển sang phần tiếp theo để xác minh việc tích hợp dịch vụ `Cart` với Amazon DynamoDB.