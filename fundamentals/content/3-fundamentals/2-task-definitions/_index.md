---
title: "Task definitions"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: "<b> 3.2. </b>"
---

### Create Task Definition for UI Service

Task Definition is a blueprint that describes how container(s) will run on Amazon ECS. It includes configurations such as container image, CPU and memory requirements, network ports to open, and required environment variables.

---
#### Create Task Definition

Export environment parameter:

```bash
export ACCOUNT_ID=11111111111 # replace with your AWS Account ID
```

Create Task Definition configuration file for UI Service with the following content:

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

Register Task Definition using AWS CLI command:

```bash
aws ecs register-task-definition --cli-input-json file://retail-store-ecs-ui-taskdef.json
```

#### Key parameters in Task Definition

| Parameter | Description |
|---------|--------|
| family | Identifier name for multiple versions of task definition, specified with version number |
| requiresCompatibilities | Determines where Amazon ECS launches tasks or services based on specified launch type |
| taskRoleArn | IAM role that allows containers to call specified AWS APIs on your behalf |
| executionRoleArn | IAM role that grants permissions for Amazon ECS container agent to make AWS API calls on your behalf |
| networkMode | Docker networking mode for containers in the task. With ECS Fargate, only awsvpc mode is supported |
| cpu, memory | For Fargate, you need to use valid CPU and memory combinations at task level |
| containerDefinitions | Detailed information such as container image, port mappings and health check configuration for containers to be used in ECS task |

---
### Verify Task Definition

View the details of created Task Definition using command:

```bash
aws ecs describe-task-definition --task-definition retail-store-ecs-ui
```