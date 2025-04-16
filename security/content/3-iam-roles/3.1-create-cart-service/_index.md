---
title: "Create Cart Service"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: "<b> 3.1. </b>"
---

In this section, we will implement the `Carts` microservice. This service provides a dedicated API for managing user shopping carts, with data persistence handled by [Amazon DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html).

![Create cart service](/images/3-iam-roles/3.1-create-cart-service/ECS-Lab-Networking-cart-service.png)
*Figure 1. Create cart service*

#### Create the ECS Task Definition

First, create the ECS task definition for the Carts service:

```bash
cat << EOF > retail-store-ecs-carts-taskdef.json
{
   "family": "retail-store-ecs-carts",
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
         "image": "public.ecr.aws/aws-containers/retail-store-sample-cart:0.8.0",
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
               "name": "CARTS_DYNAMODB_TABLENAME",
               "value": "retail-store-ecs-carts"
            },
            {
               "name": "SPRING_PROFILES_ACTIVE",
               "value": "dynamodb"
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
               "awslogs-stream-prefix": "carts-service"
            }
         }
      }
   ]
}
EOF

aws ecs register-task-definition --cli-input-json file://retail-store-ecs-carts-taskdef.json
```

The task definition includes a `taskRoleArn` parameter that references an existing IAM role. We will examine this role's permissions later.

#### Deploy the Carts Service

Create the ECS service using the following command:

```bash
aws ecs create-service \
  --cluster retail-store-ecs-cluster \
  --service-name carts \
  --task-definition retail-store-ecs-carts \
  --desired-count 1 \
  --launch-type FARGATE \
  --network-configuration "awsvpcConfiguration={subnets=[${PRIVATE_SUBNET1}, ${PRIVATE_SUBNET2}], securityGroups=[$CART_SG_ID],assignPublicIp=DISABLED}" \
  --service-connect-configuration '{
     "enabled": true,
     "namespace": "retailstore.local",
     "services": [
           {
              "portName": "application",
              "discoveryName": "carts",
              "clientAliases": [
                 {
                       "port": 80,
                       "dnsName": "carts"
                 }
              ]
           }
     ]
  }'
```

#### Monitor and Troubleshoot Deployment

1. Navigate to the [ECS console](https://console.aws.amazon.com/ecs/v2/clusters/retail-store-ecs-cluster/services/carts/tasks) to monitor the deployment.

2. If the deployment status remains **In progress**, filter for **Stopped** tasks to investigate failures.

![Failed tasks console](/images/3-iam-roles/3.1-create-cart-service/image.png)

3. Review the container logs in the **Logs** tab:

![Logs from failed task](/images/3-iam-roles/3.1-create-cart-service/image-1.png)

You may encounter an error message similar to:

```bash
User: arn:aws:sts::XXXXXXXXXXXX:assumed-role/retailStoreEcsTaskRole/172891fb75674ba998f05e9fe855fc74
is not authorized to perform: dynamodb:Query on resource: arn:aws:dynamodb:us-west-2:XXXXXXXXXXXX:table/retail-store-ecs-carts/index/idx_global_customerId
because no identity-based policy allows the dynamodb:Query action
```

This error indicates insufficient IAM permissions for accessing the DynamoDB table. Review the current permissions in the [IAM console](https://console.aws.amazon.com/iam/home#/roles/details/retailStoreEcsTaskRole?section=permissions).