---
title: "Amazon ECS Network Mode"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: "<b> 2. </b>"
---

{{% notice info %}}
Complete the [Fundamentals](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/fundamentals/) chapter before proceeding with this lab.
{{% /notice %}}

Container networking configuration is a critical aspect when running containers on a host. This section focuses on the **AWSVPC network mode**, which is mandatory for Amazon ECS on Fargate. For detailed information about choosing network modes, refer to the [Amazon ECS documentation](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-networking.html).

With **AWSVPC mode**, Amazon ECS creates and manages an Elastic Network Interface (ENI) for each task. Every task receives a dedicated private IP address within your VPC, enabling fine-grained control over task and service communications. AWSVPC network mode supports both Amazon EC2 and Fargate launch types. Learn more about [AWSVPC networking](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-networking-awsvpc.html).

{{% notice info %}}
AWSVPC network mode is mandatory for Amazon ECS tasks running on Fargate.
{{% /notice %}}

![network mode awsvpc](/images/2-network-mode/image.png)
*Figure 1. Network mode AWSVPC*

#### Examining Network Configuration in Amazon ECS Cluster

Navigate to the Amazon ECS console to inspect the service configuration:

[Amazon ECS Console](https://console.aws.amazon.com/ecs/v2/clusters/retail-store-ecs-cluster/tasks)

![Cluster Task List](/images/2-network-mode/image-1.png)
*Figure 2. Cluster Task List*

Select any running task and scroll to the **Configuration** section to view:
- Network mode
- ENI ID
- Private IP address

![Cluster Task Details](/images/2-network-mode/image-2.png)
*Figure 3. Cluster Task Details*

To retrieve task information programmatically, execute this AWS CLI command for the `ui` service:

```bash
aws ecs describe-tasks \
  --cluster retail-store-ecs-cluster \
  --tasks $(aws ecs list-tasks --cluster retail-store-ecs-cluster --service ui --query 'taskArns[0]' --output text)
```

Sample output:

```json
{
    "tasks": [
        {
            "attachments": [
                {
                    "id": "464044b3-626f-44da-86ec-fa20a064d408",
                    "type": "ElasticNetworkInterface",
                    "status": "ATTACHED",
                    ...
                }
            ],
            ...
            "networkInterfaces": [
                {
                    "attachmentId": "464044b3-626f-44da-86ec-fa20a064d408",
                    "privateIpv4Address": "10.0.4.128"
                }
            ],
            "healthStatus": "HEALTHY",
            "cpu": "0"
        }
    ]
}
```