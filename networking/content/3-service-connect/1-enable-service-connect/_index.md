---
title: "Enabling Service Connect"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: "<b> 3.1. </b>"
---

This section demonstrates how to enable Amazon ECS Service Connect by deploying two additional microservices that interact with the UI service:

![AWS ECS service Connect Architecture](https://static.us-east-1.prod.workshops.aws/public/fe1738fc-3d5c-4d22-bac3-0be10a3ad36f/static/images/60-networking/service-connect-architecture.png)
*Figure 1. AWS ECS Service Connect Architecture*

#### Deploy the Assets service

First, create an ECS task definition for the Assets service:

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

Next, create the Assets ECS service with Service Connect enabled:

{{% notice warning %}}
`$ASSET_SG_ID` should by assigned, If not, the script will create service without security group.
{{% /notice %}}

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

The Service Connect configuration:
- Enables service discovery and connectivity
- Defines a shared namespace for service communication
- Configures service aliases and port mappings

#### Deploy the Catalog service

Create the Catalog service task definition:

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

Create the Catalog ECS service with Service Connect enabled:

{{% notice warning %}}
`$CATALOG_SG_ID` should by assigned, If not, the script will create service without security group.
{{% /notice %}}

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

#### Update the UI service

Wait for the new services to deploy (approximately 2 minutes):
```bash
aws ecs wait services-stable --cluster retail-store-ecs-cluster --services catalog
aws ecs wait services-stable --cluster retail-store-ecs-cluster --services assets
```

Add environment variables to connect the UI service with Catalog and Assets services:

```json
"environment": [
    {
        "name": "ENDPOINTS_CATALOG",
        "value": "http://catalog"
    },
    {
        "name": "ENDPOINTS_ASSETS", 
        "value": "http://assets"
    }
]
```

Update the UI task definition and service:

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

Now update the UI service with the new task definition (~ 5 min):

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

echo "Waiting for service to stabilize..."

aws ecs wait services-stable --cluster retail-store-ecs-cluster --services ui
```

#### Access the Web Application

View the enhanced application with product images:

```bash
export RETAIL_ALB=$(aws elbv2 describe-load-balancers --name retail-store-ecs-ui \
 --query 'LoadBalancers[0].DNSName' --output text)

echo http://${RETAIL_ALB}
```

Access the application URL in your web browser to see the complete interface with product images:

![alt text](/images/3-service-connect/1-enable-service-connect/image.png)
*Figure 2. Web Application Interface*