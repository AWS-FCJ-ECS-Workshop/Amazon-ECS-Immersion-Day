---
title : "Trigger Auto Scaling"
date :  "`r Sys.Date()`" 
weight : 4
chapter : false
pre: "<b> 4. </b>"
---

This section demonstrates how to generate synthetic load for the UI service to observe Amazon ECS Auto Scaling behavior.

![alt text](/images/4-trigger-auto-scaling/trigger-auto-scaling.png)
*Figure 1. Triggering scaling based on Application Load Balancer request count*

#### Verify Load Balancer DNS Name

Retrieve the DNS name of the Application Load Balancer associated with the UI service. This environment variable should be available from the previous setup.

```bash
export RETAIL_ALB=$(aws elbv2 describe-load-balancers --name retail-store-ecs-ui \
  --query 'LoadBalancers[0].DNSName' --output text)
echo ${RETAIL_ALB}
```

#### Generate Load

Use the [hey tool](https://github.com/rakyll/hey) to simulate traffic by sending requests to the UI service's /home endpoint:

```bash
hey -n 1000000 -c 5 -q 40 http://$RETAIL_ALB/home &
```

#### Monitor Scaling Activity

Auto Scaling triggers when the high alarm for the scaling metric exceeds the threshold for 3 consecutive 1-minute periods. Monitor the alarm state with this command (approximately 4 minutes):

```bash
sleep 90 && aws cloudwatch wait alarm-exists --alarm-name-prefix \
 TargetTracking-service/retail-store-ecs-cluster/ui-AlarmHigh --state-value ALARM
```

View the service's task count as it scales out from 2 instances:

```bash
aws ecs describe-tasks \
    --cluster retail-store-ecs-cluster \
    --tasks $(aws ecs list-tasks --cluster retail-store-ecs-cluster --query 'taskArns[]' --output text) \
    --query "tasks[*].[group, launchType, lastStatus, healthStatus, taskArn]" --output table
```

![alt text](/images/4-trigger-auto-scaling/image.png)
*Figure 2. Task count after scale-out operation*

The CloudWatch console displays the high alarm transitioning to ALARM state:

![alt text](/images/4-trigger-auto-scaling/image-1.png)
*Figure 3. High alarm in ALARM state*

Monitor scaling activities in the UI Service Events tab, which shows the increased desired count:

![alt text](/images/4-trigger-auto-scaling/image-2.png)
*Figure 4. UI Service Events showing scaling activity*

#### Stop Load Generation

Terminate the hey process:

```bash
pkill -9 hey
```

#### Scale Down

While the service typically scales down automatically after several minutes, expedite the process with these commands:

```bash
aws ecs wait services-stable --cluster retail-store-ecs-cluster --services ui

aws ecs update-service \
    --cluster retail-store-ecs-cluster \
    --service ui \
    --task-definition retail-store-ecs-ui \
    --desired-count 2
```

![alt text](/images/4-trigger-auto-scaling/image-3.png)
*Figure 5. Service scaled down to 2 tasks*