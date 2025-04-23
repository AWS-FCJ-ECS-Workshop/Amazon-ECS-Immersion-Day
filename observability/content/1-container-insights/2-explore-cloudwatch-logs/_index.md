+++
title = "Explore CloudWatch Logs"
date = 2020-05-14T00:38:32+07:00
weight = 1
chapter = false
pre = "<b>1.2 </b>"
+++

Let's review the logs from the retail store cluster's applications and check the logs related to this cluster's performance.

### Explore Application Log

We configured the log settings for the UI container service in the [ECS task definition section](https://catalog.workshops.aws/ecs-immersion-day/en-US/30-basic/200-task-definition/) as follows. We used the default _awslogs_ driver to collect logs from the container and a **log group**, which represents a group of log streams that share the same retention, monitoring, and access control settings.

```
"logConfiguration": {
    "logDriver": "awslogs",
    "options": {
        "awslogs-group": "retail-store-ecs-tasks",
        "awslogs-region": "$AWS_REGION",
        "awslogs-stream-prefix": "ui-service"
    }
}
```

Navigate to the **Log groups** menu in **CloudWatch**. Search for the **retail-store-ecs-tasks** log group and click on it to view the UI and catalog-related log streams as shown below.

![image-1](/images/2/image-1.png?width=90pc)
![image-2](/images/2/image-2.png?width=90pc)

{{% notice note %}}
A **log stream** comprises a series of log events originating from the same source. Each distinct source of logs within CloudWatch Logs constitutes its own log stream.
{{% /notice %}}

Move to the **Logs Insights** for analyzing application log data with CloudWatch Logs Insights. Select **retail-store-ecs-tasks** for the log group and run the following query. As a result, you can retrieve the logs related to the UI service. The results will be displayed as a bar graph of log events in this log group over time.

```
fields @timestamp, @logStream, @message
| filter @logStream like /catalog-service/
| sort @timestamp desc
| limit 10
```

![image-3](/images/2/image-3.png?width=90pc)
![image-4](/images/2/image-4.png?width=90pc)
