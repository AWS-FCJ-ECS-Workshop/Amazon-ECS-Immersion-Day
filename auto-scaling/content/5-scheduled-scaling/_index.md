---
title : "Scheduled Scaling"
date :  "`r Sys.Date()`" 
weight : 5
chapter : false
pre: "<b> 5. </b>"
---

{{% notice info %}}
Important
You must have completed the following chapters as pre-requisites for this lab:
[Target Tracking Scaling](/3-target-tracking-scaling)
{{% /notice %}}

This section demonstrates how to configure Amazon ECS Service Auto Scaling using scheduled scaling policies. Scheduled scaling allows you to automatically adjust your service capacity based on predictable load patterns using time-based rules. We will configure the UI service to scale out and in using cron expressions.

![Scale out vs scale in](/images/5-scheduled-scaling/image-3.png)
*Figure 1. Scale out vs scale in*

#### Testing Scheduled Scaling

For demonstration purposes, we will create scaling actions that execute 2 minutes after command execution. In production environments, schedule your scaling actions according to consistent workload patterns.

Note: The UI service was previously registered as a scalable target with Application Auto Scaling in the Target Tracking section.

Execute the following command to increase the minimum capacity from 2 to 5 tasks in 2 minutes:

```bash
aws application-autoscaling put-scheduled-action \
 --service-namespace ecs \
 --scalable-dimension ecs:service:DesiredCount \
 --resource-id service/retail-store-ecs-cluster/ui \
 --scheduled-action-name "test-scale-up" \
 --schedule "at($(date -u -d "+2 minutes" "+%Y-%m-%dT%H:%M:%S"))" \
 --scalable-target-action MinCapacity=5,MaxCapacity=10
```

Set a timer to verify the scheduled action:

```bash
echo "Starting timer for 2 plus minutes..." && sleep 150 && echo "Time's up! and you can run the next command"
```

After the timer completes, verify the scaling action by checking the ECS tasks:

![Test scale out](/images/5-scheduled-scaling/image-1.png)
*Figure 2. Test scale out*

```bash
aws ecs describe-tasks \
 --cluster retail-store-ecs-cluster \
 --tasks $(aws ecs list-tasks --cluster retail-store-ecs-cluster --service-name ui --query 'taskArns[]' --output text) \
 --query "tasks[*].[group, launchType, lastStatus, healthStatus, taskArn]" --output table
```

Scale back to the original capacity by executing:

```bash
aws application-autoscaling put-scheduled-action \
 --service-namespace ecs \
 --scalable-dimension ecs:service:DesiredCount \
 --resource-id service/retail-store-ecs-cluster/ui \
 --scheduled-action-name "test-scale-in" \
 --schedule "at($(date -u -d "+2 minutes" "+%Y-%m-%dT%H:%M:%S"))" \
 --scalable-target-action MinCapacity=2,MaxCapacity=10
```

![Test scale in](/images/5-scheduled-scaling/image-2.png)
*Figure 3. Test scale in*

#### Production Scheduled Scaling

For production workloads, configure scaling actions based on your application's usage patterns. The following examples demonstrate scheduling for a global application with peak usage times at 9 AM UTC and 6 PM UTC daily.

![Real-world scheduled scaling](/images/5-scheduled-scaling/image.png)
*Figure 4. Real-world scheduled scaling*

Scale out to 5 tasks at 9:00 AM UTC on weekdays:

```bash
aws application-autoscaling put-scheduled-action \
    --service-namespace ecs \
    --scalable-dimension ecs:service:DesiredCount \
    --resource-id service/retail-store-ecs-cluster/ui \
    --scheduled-action-name "week-day-scale-out" \
    --schedule "cron(0 9 ? * MON-FRI *)" \
    --scalable-target-action MinCapacity=5,MaxCapacity=10
```

Scale in to 2 tasks at 6:00 PM UTC on weekdays:

```bash
aws application-autoscaling put-scheduled-action \
    --service-namespace ecs \
    --scalable-dimension ecs:service:DesiredCount \
    --resource-id service/retail-store-ecs-cluster/ui \
    --scheduled-action-name "week-day-scale-in" \
    --schedule "cron(0 18 ? * MON-FRI *)" \
    --scalable-target-action MinCapacity=2,MaxCapacity=10
```

{{% notice info %}}
Note: Due to time zone differences, these production examples may not execute during the workshop.
{{% /notice %}}