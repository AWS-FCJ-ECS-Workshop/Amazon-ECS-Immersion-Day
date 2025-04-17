---
title: "GuardDuty Runtime Monitoring"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: "<b> 4.3. </b>"
---

GuardDuty Runtime Monitoring enables security monitoring of tasks running within Amazon ECS clusters on AWS Fargate through a fully-managed security agent. When enabled, GuardDuty automatically deploys this security agent to monitor new Fargate tasks in your ECS clusters.

#### Security Agent Deployment Through Sidecar Containers

For each new Fargate task or service, GuardDuty automatically attaches a sidecar container to every container within the ECS Fargate task. The GuardDuty security agent runs within this sidecar container, collecting runtime events from all containers within these tasks.

{{% notice note %}}
Runtime Monitoring only applies to new Fargate tasks. Existing running tasks will not receive the GuardDuty sidecar container due to the immutable nature of Fargate tasks.
{{% /notice %}}

To verify the GuardDuty agent deployment:

1. Navigate to the Amazon ECS console
2. Select the `ui` service tasks
3. Locate the containers section
4. Verify the presence of `aws-guardduty-agent` running alongside the `application` container

[Access Amazon ECS Console](https://console.aws.amazon.com/ecs/v2/clusters/retail-store-ecs-cluster/services/ui/tasks)

![Guardduty ECS SideCard Container](/images/4-amazon-guardduty/4.3-guardduty-monitoring/image.png)
*Figure 1. Guardduty ECS SideCard Container*

The next section demonstrates GuardDuty's detection capabilities by deploying a test ECS task that simulates unauthorized activity.

Additional Resources:
- [Runtime Monitoring Documentation](https://docs.aws.amazon.com/guardduty/latest/ug/how-runtime-monitoring-works-ecs-fargate.html)
- [Post-Configuration Details](https://docs.aws.amazon.com/guardduty/latest/ug/runtime-monitoring-after-configuration.html)