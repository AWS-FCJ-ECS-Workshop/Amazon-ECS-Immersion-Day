---
title: "Services"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: "<b> 3.3. </b>"
---

### Amazon ECS Services

Amazon ECS Services allows you to run and maintain a specific number of task definitions in an Amazon ECS cluster. When a task fails or stops, the ECS service scheduler automatically launches new tasks to replace them, ensuring the desired number of tasks is maintained and high availability for applications.

---
### Overview

ECS Services are commonly used to:
- Manage long-running applications
- Deploy microservices
- Operate software components requiring high availability

Services can integrate with Elastic Load Balancing (ELB) to distribute traffic evenly among tasks, enabling flexible deployment, management, and scaling of containerized applications.

#### Environment Setup

Before creating a service, environment variables need to be set up:

1. Get Private Subnet ID from [ecs-lab-vpc](console.aws.amazon.com/vpcconsole/home#vpcs:search=ecs-lab-vpc)
2. Get Security Group ID from [UI-SG-ID](console.aws.amazon.com/vpcconsole/home#SecurityGroups:v=3;search=:ecs-lab-ui-sg)

```bash
# Example:
export PRIVATE_SUBNET1=subnet-04c8f4dd1af5a2897
export PRIVATE_SUBNET2=subnet-01f19d81411b79a35
export UI_SG_ID=sg-060b0549a0d6c18db
```

#### Create ECS Service

Execute the following commands to create the service:

```bash
export UI_TARGET_GROUP_ARN=$(aws elbv2 describe-target-groups --names ui-application \
 --query 'TargetGroups[0].TargetGroupArn' --output text)

aws ecs create-service \
 --cluster retail-store-ecs-cluster \
 --service-name ui \
 --task-definition retail-store-ecs-ui \
 --desired-count 2 \
 --launch-type FARGATE \
 --load-balancers targetGroupArn=${UI_TARGET_GROUP_ARN},containerName=application,containerPort=8080 \
 --network-configuration "awsvpcConfiguration={subnets=[${PRIVATE_SUBNET1},${PRIVATE_SUBNET2}],securityGroups=[${UI_SG_ID}],assignPublicIp=DISABLED}"
```

#### Check Service Status

Monitor service status through [Amazon ECS Console](https://console.aws.amazon.com/ecs/v2/clusters/retail-store-ecs-cluster/services/ui)

![alt text](/images/3-fundamentals/3-services/image-2.png)

Or wait for service stabilization using AWS CLI:

```bash
aws ecs wait services-stable --cluster retail-store-ecs-cluster --services ui
```

View running tasks list:

```bash
aws ecs describe-tasks \
    --cluster retail-store-ecs-cluster \
    --tasks $(aws ecs list-tasks --cluster retail-store-ecs-cluster --query 'taskArns[]' --output text) \
    --query "tasks[*].[group, launchType, lastStatus, healthStatus, taskArn]" --output table
```

#### Access Application

Get load balancer URL:

```bash
export RETAIL_ALB=$(aws elbv2 describe-load-balancers --name retail-store-ecs-ui \
 --query 'LoadBalancers[0].DNSName' --output text)

echo http://${RETAIL_ALB}
```

Access the URL in browser to view the application:

![alt text](/images/3-fundamentals/3-services/image-3.png)

---
### Current Architecture

After completing the above steps, the service architecture will be as follows:

![alt text](/images/3-fundamentals/3-services/ECS-Lab-Networking-Current-Architecture.png)