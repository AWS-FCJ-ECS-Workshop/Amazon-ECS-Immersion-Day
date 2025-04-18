---
title: "Amazon EFS Volume with Fargate"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: "<b> 2. </b>"
---

This section demonstrates how to mount an Amazon EFS volume to the **Assets service** and update product images.

![alt text](/images/2-efs-volume-with-fargate/ECS-Lab-Networking-EFS.png)
*Figure 1. Current images asset folder*

To mount an EFS volume to the Assets application container, add the following mount point configuration to the task definition:
```json
    "mountPoints": [{
        "sourceVolume": "efsVolume",
        "containerPath": "/usr/share/nginx/html/assets"
    }]
```

Key configuration elements:

*   `sourceVolume`: The name of the EFS volume to mount
*   `containerPath`: The mount point location within the Assets container (**_/usr/share/nginx/html/assets_**) where product images are stored

Add the EFS volume configuration to the task definition:

```json
    "volumes": [{
        "name": "efsVolume",
        "efsVolumeConfiguration": {
            "fileSystemId": "$EFS_ID", // Add your EFS_ID environment
            "rootDirectory": "/",
            "transitEncryption": "ENABLED",
            "authorizationConfig": {
                "iam": "ENABLED"
            }
        }
    }]
```

Configure the task role for interactive session verification:

```json
    "taskRoleArn": "arn:aws:iam::${ACCOUNT_ID}:role/retailStoreEcsTaskRole",
```

Update the ECS task definition for the Assets service:

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

Update the Assets service to maintain two tasks for high availability (approximately 5 minutes):

```bash
    aws ecs update-service \
    --cluster retail-store-ecs-cluster \
    --service assets \
    --desired-count 2 \
    --task-definition retail-store-ecs-assets \
    --enable-execute-command \
    --force-new-deployment
    
    echo "Waiting for service to stabilize..."
    
    aws ecs wait services-stable --cluster retail-store-ecs-cluster --services assets
```

![alt text](/images/2-efs-volume-with-fargate/ECS-Lab-Networking-EFS-mounting.png)
*Figure 2. EFS Volume mounted to the Assets Service*

The EFS volume is now mounted to the Assets service tasks running on the ECS Fargate cluster. Proceed to the next section for configuration verification.