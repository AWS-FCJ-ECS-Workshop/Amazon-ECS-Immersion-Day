---
title : "Introduction"
date :  "`r Sys.Date()`" 
weight : 1
chapter : false
pre: "<b> 1. </b>"
---


#### What is Horizontal Scaling?

Horizontal scaling refers to the process of **increasing system capacity by adding more computing resources** to handle growing workload demands. In Amazon Elastic Container Service (Amazon ECS), microservices can automatically scale using [Application Auto Scaling](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/service-auto-scaling.html). This scaling is based on real-time metrics collected by [Amazon ECS service utilization monitoring](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/cloudwatch-metrics.html#service_utilization), which tracks CPU and memory consumption of your tasks.

![Horizontal scaling](/images/1-introduction/image.png)
*Figure 1: Horizontal scaling architecture in Amazon ECS*

#### Cost Considerations

AWS Auto Scaling is available at no additional cost. You only pay for:
- The AWS resources consumed by your applications
- Amazon CloudWatch monitoring fees

{{% notice note %}}
This workshop follows the pricing model outlined in the [Fundamentals](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/fundamentals/) chapter. In this lab, services will automatically scale up to a maximum of 10 tasks. The cost estimates provided below are based on this scaling limit.
{{% /notice %}}

#### Estimated Costs

| Service | Purpose | Monthly Cost (USD) | Annual Cost (USD) |
|---------|----------|-------------------|-------------------|
| **AWS Fargate** | Container task execution | $9.01 | $108.12 |

These estimates help you understand the potential costs when implementing horizontal scaling with Amazon ECS and AWS Fargate. Actual costs may vary based on your specific usage patterns and requirements.