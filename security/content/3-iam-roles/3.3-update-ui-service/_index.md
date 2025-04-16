---
title: "Update UI Services"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: "<b> 3.3. </b>"
---

In this section, we will update the `UI` service to integrate with the new `Carts` service. This integration will enable the Retail Store Sample application to store users' shopped items in the [Amazon DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html)  database by leveraging the `Carts` service.

To link the `UI` service with the `Carts` service, we need to add the following environment variable to the UI task definition:

```json
    "environment": [
        {
            "name": "ENDPOINTS_CARTS",
            "value": "http://carts"
        }
    ]
```

Let's update the **UI task definition** with the new environment variable:

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
    

Now, let's update the ECS service to use the latest task definition revision (**~ 6 min**):

```bash
    aws ecs update-service \
        --cluster retail-store-ecs-cluster \
        --service ui \
        --task-definition retail-store-ecs-ui
    
    echo "Waiting for service to stabilize..."
    
    aws ecs wait services-stable --cluster retail-store-ecs-cluster --services ui
```

![Deployment successfully](/images/3-iam-roles/3.3-update-ui-service/image.png)
*Figure 1. Deployment successfully*


Once the deployment is complete, proceed to the next section to test the `Cart` service integration with Amazon DynamoDB.