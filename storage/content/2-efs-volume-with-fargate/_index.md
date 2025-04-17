---
title: "Amazon EFS Volume with Fargate"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: "<b> 2. </b>"
---

In this section, we will walk through the process of mounting an Amazon EFS volume to the **Assets service** and updating the product images.
---
<!-- Understand about Amazon EFS Volume & images -->

To mount the EFS volume to the Assets application container, we need to add the following mount point configuration to the task definition:
```json
    "mountPoints": [{
        "sourceVolume": "efsVolume",
        "containerPath": "/usr/share/nginx/html/assets"
    }]
```

Let's understand this configuration:

*   `sourceVolume`: Specifies the name of the EFS volume to be mounted
*   `containerPath`: Defines the mount point within the Assets container where the EFS volume will be attached. In this case, it's **_/usr/share/nginx/html/assets_**, which is the directory for storing product images.

We also need to define the EFS volume configuration in the task definition:

```json
    "volumes": [{
        "name": "efsVolume",
        "efsVolumeConfiguration": {
            "fileSystemId": "$EFS_ID",
            "rootDirectory": "/",
            "transitEncryption": "ENABLED",
            "authorizationConfig": {
                "iam": "ENABLED"
            }
        }
    }]
```

Since we'll need to verify the EFS volume mount through an interactive session later, we must configure the task role:

```json
    "taskRoleArn": "arn:aws:iam::${ACCOUNT_ID}:role/retailStoreEcsTaskRole",
```

Let's update the ECS task definition for the Assets service:

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
    

Now, let's update the Assets service. The following command will update the service to maintain two tasks using the new task definition, eliminating single points of failure (**~ 5 min**):

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

With these steps completed, the EFS volume is now successfully mounted to the Assets service tasks running on the ECS Fargate cluster. We can proceed to verify the configuration in the next phase.