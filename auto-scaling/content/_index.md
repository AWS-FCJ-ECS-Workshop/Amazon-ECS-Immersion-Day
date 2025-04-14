---
title : "Auto Scaling with Amazon ECS"
date :  "`r Sys.Date()`" 
chapter : false
---

# Auto Scaling with Amazon ECS

{{% notice info %}}
**Prerequisites**: Complete the [Fundamentals](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/fundamentals/) chapter before starting this lab.
{{% /notice %}}

Building upon our previous setup of the base environment and ECS cluster, this lab explores how to implement auto scaling effectively with Amazon Elastic Container Service (Amazon ECS).

### Understanding Auto Scaling in Amazon ECS

#### Resource Allocation in Fargate

In Amazon ECS with Fargate, each task runs in its own isolated environment. When creating task definitions, you must specify:
- CPU units
- Memory allocation

Proper resource allocation is critical for optimal performance. Your tasks should have sufficient resources to:
- Handle expected workloads
- Maintain desired performance levels
- Process requests efficiently

#### Right-sizing Your Tasks

To determine the appropriate resource allocation:
1. Conduct performance measurements
2. Execute comprehensive load testing
3. Monitor key performance metrics
4. Analyze resource utilization patterns

If tasks exhibit performance issues or resource constraints, adjust the CPU and memory allocations accordingly.

#### Horizontal Scaling

After optimizing individual task resources, implement horizontal scaling by adding more task instances. This approach is recommended for cloud-native containerized applications because it:
- Improves availability
- Enhances fault tolerance
- Distributes workload effectively

#### Service Auto Scaling

Amazon ECS provides automated task scaling through Service Auto Scaling, which leverages [Application Auto Scaling](https://docs.aws.amazon.com/autoscaling/application/userguide/what-is-application-auto-scaling.html). 

Key considerations for effective auto scaling:
- Use proportional metrics
- Ensure metrics scale linearly with load
- Configure appropriate scaling thresholds

For example: If your service's load doubles while maintaining the same number of tasks, your chosen metric should also double.

{{% notice note %}}
When using Amazon ECS with EC2 instances, consider implementing capacity providers to manage EC2 instance capacity. This lab focuses on Fargate deployments and does not cover EC2 capacity providers.
{{% /notice %}}

{{< youtube YDbqnZ32NdM >}}