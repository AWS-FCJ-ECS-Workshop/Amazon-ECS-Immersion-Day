---
title: "Update Services"
date: "`r Sys.Date()`"
weight: 4
chapter: false
pre: "<b> 3.4 </b>"
---

### Update a Service

In this section, you will learn how to update an ECS service. This process is very useful in situations like changing container images or adjusting configurations.

---
### Introduction to Environment Variables

Environment variables are one of the main mechanisms used to configure container workloads, regardless of the orchestrator. In this practice, you will adjust the UI service configuration by passing a new environment variable to change the workload behavior. Specifically, you will use the `RETAIL_UI_BANNER` setting to add a banner to the page.

In ECS task definitions, environment variables are declared with name and value as follows:

```json
"environment": [
    {
        "name": "RETAIL_UI_BANNER", 
        "value": "We've updated the UI service!"
    }
]
```

#### Update Task Definition with Environment Variable

Create a new task definition file with environment variable:

```bash
cat << EOF > retail-store-ecs-ui-updated-taskdef.json
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
            # --- New Environment ---
            "environment": [
                {
                    "name": "RETAIL_UI_BANNER",
                    "value": "We've updated the UI service!"
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
```

#### Register New Task Definition

Use the `register-task-definition` command to update the task definition:

```bash
aws ecs register-task-definition --cli-input-json file://retail-store-ecs-ui-updated-taskdef.json
```

{{% notice info %}}
**Important note**: ECS task definitions are immutable, meaning they cannot be modified after creation. Instead, the above command will create a new version of the task definition with new parameters replacing existing ones.
{{% /notice %}}

Check task definition versions:

```bash
aws ecs list-task-definitions --family-prefix retail-store-ecs-ui
```

The result will show all versions of the UI task definition family:

```json
{
    "taskDefinitionArns": [
        "arn:aws:ecs:us-west-2:XXXXXXXXXXXX:task-definition/retail-store-ecs-ui:1",
        "arn:aws:ecs:us-west-2:XXXXXXXXXXXX:task-definition/retail-store-ecs-ui:2"
    ]
}
```

#### Update Service

Update the ECS service to use the new task definition version:

```bash
aws ecs update-service --cluster retail-store-ecs-cluster --service ui --task-definition retail-store-ecs-ui
```

Wait for ECS to deploy the changes (about 5 minutes):

```bash
aws ecs wait services-stable --cluster retail-store-ecs-cluster --services ui
```

![alt text](/images/3-fundamentals/4-update-service/image-1.png)

Refresh your browser, you will see the banner has been added based on environment variables:

![alt text](/images/3-fundamentals/4-update-service/image-2.png)

{{% notice note %}}
**Tip**: You may need to scroll up to see the banner.
{{% /notice %}}