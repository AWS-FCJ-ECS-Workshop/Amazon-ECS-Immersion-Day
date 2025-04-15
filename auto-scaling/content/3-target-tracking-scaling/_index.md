---
title : "Target Tracking Scaling"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre: "<b> 3. </b>"
---

#### Target Tracking Scaling

![alt text](/images/3-target-tracking-scaling/image-2.png)
*Figure 1: Service scaling*

Configure Amazon ECS Service Auto Scaling using Target Tracking Scaling to automatically adjust the number of tasks based on application demand.

Register the UI service as a scalable target with Application Auto Scaling by setting the scaling range between 2 and 10 tasks:

```bash
aws application-autoscaling register-scalable-target \
    --service-namespace ecs \
    --scalable-dimension ecs:service:DesiredCount \
    --resource-id service/retail-store-ecs-cluster/ui \
    --min-capacity 2 \
    --max-capacity 10
```

Next, create and apply a scaling policy:

1. Create a JSON configuration file defining the scaling policy. This policy uses Application Load Balancer request count per target metric, with a target of 1,500 requests per ECS task:

{{% notice note %}}
The scaling thresholds shown are for demonstration. In production, determine appropriate values based on your workload analysis.
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

2. Apply the scaling policy:

```bash
aws application-autoscaling put-scaling-policy \
    --service-namespace ecs \
    --scalable-dimension ecs:service:DesiredCount \
    --resource-id service/retail-store-ecs-cluster/ui \
    --policy-name ui-scaling-policy --policy-type TargetTrackingScaling \
    --target-tracking-scaling-policy-configuration file://ui-scaling-policy.json
```

#### CloudWatch Alarm Configuration

The scaling policy creates two CloudWatch alarms automatically. View these in the [CloudWatch Alarms console](https://console.aws.amazon.com/cloudwatch/home#alarmsV2:).

{{% notice note %}}
Alarm states reflect current request volumes. The UI-AlarmLow alarm triggers when requests fall below 1350.
{{% /notice %}}

![alt text](/images/3-target-tracking-scaling/image-3.png)
*Figure 2. CloudWatch Alarms Dashboard*

**Scale-out Alarm Configuration:**
- Metric threshold: ALBRequestCountPerTarget exceeds 1500
- Evaluation period: 3 consecutive data points over 3 minutes
- Action: Increases task count when in ALARM state
- Scaling behavior: Adds tasks progressively until metric normalizes or reaches maximum capacity

![alt text](/images/3-target-tracking-scaling/image.png)
*Figure 3. Service scaling out based on ALBRequestCountPerTarget metric*

**Scale-in Alarm Configuration:**
- Metric threshold: ALBRequestCountPerTarget below 1350
- Evaluation period: 15 consecutive data points over 15 minutes
- Action: Decreases task count when in ALARM state
- Scaling behavior: Gradual scale-in to ensure service stability

![alt text](/images/3-target-tracking-scaling/image-1.png)
*Figure 4. Service scaling in based on ALBRequestCountPerTarget metric*