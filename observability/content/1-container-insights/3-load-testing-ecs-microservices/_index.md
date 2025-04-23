+++
title = "Load Testing ECS Microservices"
date = 2020-05-14T00:38:32+07:00
weight = 1
chapter = false
pre = "<b>1.3 </b>"
+++

In this section, we'll generate synthetic load using the _stress_ command, which is a command-line tool for Linux-based operating systems to introduce load into the system. We will _exec_ into the _ui_ task and run the _stress_ command to trigger scaling. We will then observe the CloudWatch metrics to see the scaling effect.

### Connect to the ECS Task

Run the following command to select one of the running tasks with enableExecuteCommand enabled:

```
ECS_EXEC_TASK_ARN=$(aws ecs list-tasks --cluster retail-store-ecs-cluster \
    --service-name ui --query 'taskArns[]' --output text | \
    xargs -n1 aws ecs describe-tasks --cluster retail-store-ecs-cluster --tasks | \
    jq -r '.tasks[] | select(.enableExecuteCommand == true) | .taskArn' | \
    head -n 1)

echo $ECS_EXEC_TASK_ARN
```

This will output the ARN of the task:

`arn:aws:ecs:us-west-2:XXXXXXXXXX:task/retail-store-ecs-cluster/0564778486a846599b8bd6b544e5f6eb`

Start your /bin/bash interactive session in the running task.

```
if [ -z ${ECS_EXEC_TASK_ARN} ]; then echo "ECS_EXEC_TASK_ARN is not correctly configured!"; else
aws ecs execute-command --cluster retail-store-ecs-cluster \
    --task $ECS_EXEC_TASK_ARN \
    --container application \
    --interactive \
    --command "/bin/bash"
fi
```

### Scaling Up

First, install the stress command:

`dnf install stress -y`

Now, issue the following command to stress 10 CPU cores with a timeout of 180 seconds:

`stress -c 10 --timeout 180`

Navigate to the Container Insights console page and select the time range to 5 minutes as shown below:

{{% notice note %}}
It will take a few minutes for the metrics to appear in CloudWatch Insights. To observe the stress spike in real-time, enable auto-refresh for 10 seconds in the CloudWatch console.
{{% /notice %}}

![image-5](/images/2/image-5.png?width=90pc)

On the Container Insights console page, you can observe that the CPU utilization increases as the load on the _ui_ service is increased.

![image-6](/images/2/image-6.png?width=90pc)

After observing the scaling effect, terminate your session:

`exit`
