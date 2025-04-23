+++
title = "Container Insights"
date = 2020-05-14T00:38:32+07:00
weight = 1
chapter = false
pre = "<b>1. </b>"
+++

{{% notice info %}}
**Prerequisites:** Complete the [Fundamentals](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/fundamentals/) & [ECS Service Connect](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/networking/) chapter before starting this lab.
{{% /notice %}}

By default, Amazon ECS provides metrics for clusters and services. However, by opting into Container Insights, you can enhance operational convenience by gaining access to additional metrics that facilitate operational management. [CloudWatch Container Insights](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/cloudwatch-container-insights.html) collects, aggregates, and summarizes metrics and logs from your containerized applications and microservices. You can also set CloudWatch alarms on metrics that Container Insights collects.

In this lab, we will first check whether Container Insights are enabled on your Amazon ECS Cluster. If Container Insights are not enabled, we will use AWS CLI to enable them for CloudWatch.

Check the status of Container Insights for the ECS cluster by filtering the AWS CLI output from [describe-clusters](https://docs.aws.amazon.com/AmazonECS/latest/APIReference/API_DescribeClusters.html) . The output from the AWS CLI query will return the Container Insights status to indicate whether it is enabled or disabled for the ECS Cluster.

```
aws ecs describe-clusters --clusters retail-store-ecs-cluster --include SETTINGS --query 'clusters[*].settings'
```

![image-1](/images/1/image-1.png?width=90pc)

#### Expand in case the Container Insight is not enabled.

If Container Insights are not enabled, use the following AWS CLI command to enable Container Insights for CloudWatch:

```
aws ecs update-cluster-settings --cluster retail-store-ecs-cluster --settings name=containerInsights,value=enabled
```

You should receive the following output in the command line:

```
{
  "cluster": {
    "clusterArn": "arn:aws:ecs:us-west-2:XXXXXXXXXX:cluster/retail-store-ecs-cluster",
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
      "name": "containerInsights",
      "value": "enabled"
      }
    ],
    "capacityProviders": [],
    "defaultCapacityProviderStrategy": [],
    "attachments": []
  }
}
```
