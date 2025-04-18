---
title: "Amazon EFS Volume với Fargate"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: "<b> 2. </b>"
---

Phần này hướng dẫn cách gắn kết một volume Amazon EFS vào **Assets service** và cập nhật hình ảnh sản phẩm.

![alt text](/images/2-efs-volume-with-fargate/ECS-Lab-Networking-EFS.png)
*Hình 1. Thư mục asset hình ảnh hiện tại*

Để gắn kết volume EFS vào container Assets application, thêm cấu hình mount point sau vào task definition:
```json
    "mountPoints": [{
        "sourceVolume": "efsVolume",
        "containerPath": "/usr/share/nginx/html/assets"
    }]
```

Các thành phần cấu hình chính:

* `sourceVolume`: Tên của volume EFS cần gắn kết
* `containerPath`: Vị trí mount point trong container Assets (**_/usr/share/nginx/html/assets_**) nơi lưu trữ hình ảnh sản phẩm

Thêm cấu hình volume EFS vào task definition:

```json
    "volumes": [{
        "name": "efsVolume",
        "efsVolumeConfiguration": {
            "fileSystemId": "$EFS_ID", // Thêm biến môi trường EFS_ID
            "rootDirectory": "/",
            "transitEncryption": "ENABLED",
            "authorizationConfig": {
                "iam": "ENABLED"
            }
        }
    }]
```

Cấu hình task role để xác thực phiên tương tác:

```json
    "taskRoleArn": "arn:aws:iam::${ACCOUNT_ID}:role/retailStoreEcsTaskRole",
```

Cập nhật task definition ECS cho Assets service:

```bash
   cat << EOF > retail-store-ecs-asset-storage-taskdef.json
    {
        "family": "retail-store-ecs-assets",
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
                # Updated
                "mountPoints": [
                    {
                        "sourceVolume": "efsVolume",
                        "containerPath": "/usr/share/nginx/html/assets"
                    }
                ],
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
        ],
        # Updated
        "volumes": [
            {
                "name": "efsVolume",
                "efsVolumeConfiguration": {
                    "fileSystemId": "$EFS_ID",
                    "rootDirectory": "/",
                    "transitEncryption": "ENABLED",
                    "authorizationConfig": {
                        "iam": "ENABLED"
                    }
                }
            }
        ]
    }
    EOF
    
    aws ecs register-task-definition --cli-input-json file://retail-store-ecs-asset-storage-taskdef.json
```

Cập nhật Assets service để duy trì hai task cho tính sẵn sàng cao (khoảng 5 phút):

```bash
    aws ecs update-service \
    --cluster retail-store-ecs-cluster \
    --service assets \
    --desired-count 2 \
    --task-definition retail-store-ecs-assets \
    --enable-execute-command \
    --force-new-deployment
    
    echo "Đang chờ service ổn định..."
    
    aws ecs wait services-stable --cluster retail-store-ecs-cluster --services assets
```

![alt text](/images/2-efs-volume-with-fargate/ECS-Lab-Networking-EFS-mounting.png)
*Hình 2. Volume EFS được gắn kết vào Assets Service*

Volume EFS hiện đã được gắn kết vào các task của Assets service đang chạy trên cụm ECS Fargate. Tiếp tục sang phần tiếp theo để xác minh cấu hình.