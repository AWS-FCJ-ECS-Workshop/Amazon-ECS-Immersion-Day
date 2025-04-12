---
title: "Amazon ECS Cluster"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: "<b> 3.1. </b>"
---

### Building Amazon ECS Cluster

In this lab, you will create core components of Amazon ECS, including ECS Cluster, Task Definition and ECS Service. The ultimate goal is to deploy a container behind an Application Load Balancer (ALB).

#### Create Amazon ECS Cluster

Use the terminal in VS Code to create an Amazon ECS Cluster named `retail-store-ecs-cluster` with CloudWatch Container Insights enabled. Container Insights collects, aggregates, and summarizes metrics and logs from your containerized applications and microservices.

```bash
export AWS_REGION={your_region} # Replace your working region

aws ecs create-cluster --cluster-name retail-store-ecs-cluster --region $AWS_REGION --settings name=containerInsights,value=enabled
```

After executing the command, you will receive the following result, which shows that CloudWatch Container Insights has been enabled:

```json
{
    "cluster": {
        "clusterArn": "arn:aws:ecs:us-west-2:111111111111:cluster/retail-store-ecs-cluster",
        "clusterName": "retail-store-ecs-cluster",
        "status": "ACTIVE",
        "registeredContainerInstancesCount": 0,
        "runningTasksCount": 0,
        "pendingTasksCount": 0,
        "activeServicesCount": 0,
        "statistics": [],
        "tags": [],
        "settings": [
            {
                "name": "containerInsights", // containerInsights enabled
                "value": "enabled"
            }
        ],
        "capacityProviders": [],
        "defaultCapacityProviderStrategy": []
    }
}
```

### Verify Cluster

To view details about the cluster you just created:

1. Go to [Amazon ECS Console](https://console.aws.amazon.com/ecs/)
2. Find and select the cluster named `retail-store-ecs-cluster`

![alt text](/images/3-fundamentals/image-1.png)

After completing the cluster creation, proceed to the next step to create an ECS Task Definition - defining how to deploy the UI service.