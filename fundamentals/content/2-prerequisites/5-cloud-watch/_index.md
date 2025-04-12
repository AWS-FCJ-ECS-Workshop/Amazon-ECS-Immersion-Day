---
title: "CloudWatch - Application Monitoring"
date: "`r Sys.Date()`"
weight: 5
chapter: false
pre: "<b> 2.5. </b>"
---

### What is CloudWatch

Amazon CloudWatch is a comprehensive monitoring and observability service built for developers, operations engineers, site reliability engineers (SRE), and IT managers. CloudWatch provides actionable data and insights to monitor applications, respond to system-wide performance changes, optimize resource utilization, and get a unified view of operational health. CloudWatch collects operational data and monitoring in the form of logs, metrics, and events, providing a unified view of AWS resources, applications and services running on AWS and on-premises infrastructure.

### Use Case

In this lab, we will learn how to use Amazon CloudWatch to monitor metrics, logs, and traces in Amazon ECS Services.

### Create Log Group

1. Access the Amazon CloudWatch interface in AWS Management Console

2. Navigate to CloudWatch Logs Group and select Create Log Group

![alt text](/images/2-prerequisites/5-cloud-watch/image.png)

3. Enter configuration information:
- Log Group name: `retail-store-ecs-tasks`
- Add tags (optional) 
- Select Create to create Log Group

{{% notice warning %}}
Note: The Log Group name `retail-store-ecs-tasks` will be used throughout the CLI commands in this lab!
{{% /notice %}}

![alt text](/images/2-prerequisites/5-cloud-watch/image-1.png)

4. Confirm the Log Group has been created successfully

![alt text](/images/2-prerequisites/5-cloud-watch/image-2.png)