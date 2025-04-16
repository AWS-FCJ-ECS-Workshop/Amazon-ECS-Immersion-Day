---
title: "GuardDuty Runtime Monitoring"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: "<b> 4.3. </b>"
---

When you enable Runtime Monitoring, GuardDuty becomes prepared to consume runtime events from tasks. These tasks run within Amazon ECS clusters, which in turn operate on AWS Fargate (Fargate) instances. For GuardDuty to receive these runtime events, it utilizes a fully-managed, dedicated security agent.

Once enabled, GuardDuty will begin deploying the security agent to new Fargate tasks launched in your Amazon ECS clusters. [Learn more about how Runtime Monitoring works](https://docs.aws.amazon.com/guardduty/latest/ug/how-runtime-monitoring-works-ecs-fargate.html) 


#### GuardDuty adds a sidecar container

For each new Fargate task or service that starts running, GuardDuty attaches a container (sidecar) to every container within the Amazon ECS Fargate task. The GuardDuty security agent operates within this attached GuardDuty container. This approach enables GuardDuty to collect runtime events from each container running within these tasks. [Find out more about what happens after configuration](https://docs.aws.amazon.com/guardduty/latest/ug/runtime-monitoring-after-configuration.html) 

{{% notice note %}}
Note that Fargate tasks are immutable by default. GuardDuty will not deploy the sidecar to tasks that are already in a running state.
{{% /notice %}}

To examine the GuardDuty agent, navigate to the Amazon ECS console and inspect one of the running tasks of the `ui` service. Scroll down to the containers section, where you'll observe the `aws-guardduty-agent` running alongside the `application` container.

[Open Amazon ECS console](https://console.aws.amazon.com/ecs/v2/clusters/retail-store-ecs-cluster/services/ui/tasks)

![Guardduty ECS SideCard Container](/images/4-amazon-guardduty/4.3-guardduty-monitoring/image.png)
*Figure 1. Guardduty ECS SideCard Container*

In the upcoming section, we will deploy a malicious ECS task that performs an unauthorized activity to demonstrate GuardDuty's detection capabilities.
