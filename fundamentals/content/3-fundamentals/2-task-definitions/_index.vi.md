---
title: "Task definitions"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: "<b> 3.2. </b>"
---

### Tạo Task Definition cho UI Service

Task Definition là bản thiết kế mô tả cách thức container (hoặc nhiều container) sẽ chạy trên Amazon ECS. Nó bao gồm các cấu hình như container image, yêu cầu về CPU và bộ nhớ, cổng mạng cần mở, và các biến môi trường cần thiết.

---
#### Tạo Task Definition

Export tham số môi trường:

```bash
export ACCOUNT_ID=11111111111 # thay thế AWS Account ID của bạn
```

Tạo file cấu hình Task Definition cho UI Service với nội dung sau:

```json
{
    "family": "retail-store-ecs-ui",
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
    ],
    "executionRoleArn": "arn:aws:iam::${ACCOUNT_ID}:role/retailStoreEcsTaskExecutionRole", // Export environment variable
    "taskRoleArn": "arn:aws:iam::${ACCOUNT_ID}:role/retailStoreEcsTaskRole"
}
```

Đăng ký Task Definition bằng lệnh AWS CLI:

```bash
aws ecs register-task-definition --cli-input-json file://retail-store-ecs-ui-taskdef.json
```

#### Các tham số chính trong Task Definition

| Tham số | Mô tả |
|---------|--------|
| family | Tên định danh cho nhiều phiên bản của task definition, được chỉ định kèm số phiên bản |
| requiresCompatibilities | Xác định nơi Amazon ECS khởi chạy task hoặc service dựa trên launch type được chỉ định |
| taskRoleArn | IAM role cho phép container gọi các API AWS được chỉ định thay mặt bạn |
| executionRoleArn | IAM role cấp quyền cho Amazon ECS container agent thực hiện các lệnh gọi API AWS thay mặt bạn |
| networkMode | Chế độ mạng Docker cho các container trong task. Với ECS Fargate, chỉ hỗ trợ chế độ awsvpc |
| cpu, memory | Đối với Fargate, bạn cần sử dụng tổ hợp CPU và bộ nhớ hợp lệ ở cấp độ task |
| containerDefinitions | Thông tin chi tiết như container image, port mappings và cấu hình health check cho container sẽ được sử dụng trong ECS task |

---
### Kiểm tra Task Definition

Xem chi tiết Task Definition vừa tạo bằng lệnh:

```bash
aws ecs describe-task-definition --task-definition retail-store-ecs-ui
```
