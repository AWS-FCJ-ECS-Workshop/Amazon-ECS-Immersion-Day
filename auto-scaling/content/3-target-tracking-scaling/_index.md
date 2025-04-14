---
title : "Target Tracking Scaling"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre: "<b> 3. </b>"
---

#### Target Tracking Scaling

![alt text](/images/3-target-tracking-scaling/image-2.png)

*Figure 1. Service scaling based on `ALBRequestCountPerTarget` metric*

This section guides you through configuring ECS Service Auto Scaling using Target Tracking Scaling. You will learn how to set up application autoscaling and implement appropriate scaling policies for your services.

First, register the UI service as a scalable target with Application Auto Scaling. Execute the following command to set the scaling range from 2 to 10 tasks:

```bash
aws application-autoscaling register-scalable-target \
    --service-namespace ecs \
    --scalable-dimension ecs:service:DesiredCount \
    --resource-id service/retail-store-ecs-cluster/ui \
    --min-capacity 2 \
    --max-capacity 10
```

Create a scaling policy for the target by following these steps:

1. Create a JSON configuration file that defines the scaling policy. This configuration uses the ALB request count per target metric type, targeting 1,500 requests per ECS task.

{{% notice note %}}
The scaling policy values shown here are for demonstration purposes. Analyze your workload patterns to determine appropriate scaling metrics and thresholds for your environment.
{{% /notice %}}

```bash
cat << EOF > ui-scaling-policy.json
{
    "TargetValue": 1500,
    "PredefinedMetricSpecification": {
        "PredefinedMetricType": "ALBRequestCountPerTarget",
        "ResourceLabel": "$UI_ALB_PREFIX/$UI_TG_PREFIX"
    }
}
EOF
```

2. Apply the scaling policy using this command:

```bash
aws application-autoscaling put-scaling-policy \
    --service-namespace ecs \
    --scalable-dimension ecs:service:DesiredCount \
    --resource-id service/retail-store-ecs-cluster/ui \
    --policy-name ui-scaling-policy --policy-type TargetTrackingScaling \
    --target-tracking-scaling-policy-configuration file://ui-scaling-policy.json
```

#### CloudWatch Alarm Configuration

The scaling policy automatically creates two CloudWatch alarms. Access these in the [CloudWatch Alarms console](https://console.aws.amazon.com/cloudwatch/home#alarmsV2:).

{{% notice note %}}
Alarm states depend on current request volumes. For instance, **UI-AlarmLow** activates when requests drop below **1350**.
{{% /notice %}}

![alt text](/images/3-target-tracking-scaling/image-1.png)
*Figure 2. CloudWatch Alarms Dashboard*

**Scale-out (High) Alarm Configuration:**
- Metric threshold: ALBRequestCountPerTarget > 1500
- Evaluation period: 3 data points within 3 minutes
- Action: Adds tasks when ALARM state is triggered
- Scaling behavior: Incrementally adds tasks until metric stabilizes or maximum capacity is reached

**Scale-in (Low) Alarm Configuration:**
- Metric threshold: ALBRequestCountPerTarget < 1350
- Evaluation period: 15 data points within 15 minutes
- Action: Removes tasks when ALARM state is triggered
- Scaling behavior: Conservative scale-in to maintain service availability