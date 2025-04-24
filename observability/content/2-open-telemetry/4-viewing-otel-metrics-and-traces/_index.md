+++
title = "Viewing Otel Metrics and Traces"
date = 2020-05-14T00:38:32+07:00
weight = 2
chapter = false
pre = "<b>2.4 </b>"
+++

### Verifying Traces and Metrics

Let's verify your monitoring setup by accessing your application through the AWS Load Balancer. First, retrieve the Load Balancer's DNS name and use it to generate test traffic to your application.

```
RETAIL_ALB=$(aws elbv2 describe-load-balancers --name retail-store-ecs-ui \
 --query 'LoadBalancers[0].DNSName' --output text)

echo ${RETAIL_ALB}
```

Use the DNS name displayed above to access your application in a web browser. To generate meaningful traces:

1. Click on individual products to view their details
2. Open the Catalog
3. Browse through different product categories

{{% notice info %}}
If the cart component has not yet been deployed following the [Security Lab](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/security/3-iam-roles/), avoid adding items to the cart.
{{% /notice %}}

These actions should generate traces for the UI, Catalog, and Assets services. If you don't see traces for all services immediately, wait a few minutes and refresh the CloudWatch console, as there may be a slight delay in data processing.

To examine the tracing, metrics, and logs data, navigate to the CloudWatch console using the link below.

{{% button href="https://console.aws.amazon.com/cloudwatch/home#container-insights:infrastructure" %}}Open Amazon CloudWatch console{{% /button %}}

### Check AWS X-Ray Trace Map

In CloudWatch, go to **X-Ray traces** and select **Trace map**. You should see a service map similar to the one shown below.

![image](/images/5/image.png?width=90pc)

The Trace Map displays the relationships and interactions between your microservices and components. This visualization helps you understand how requests flow through your application architecture. You'll notice the name ui-application in the tracing map, which corresponds to the _OTEL_SERVICE_NAME_ we configured earlier.

Below the Trace Map is the **Segment Timeline**, which shows the different Segments the trace contains.

![image-1](/images/5/image-1.png?width=90pc)
![image-2](/images/5/image-2.png?width=90pc)

{{% notice info %}}
To learn more about AWS X-Ray concepts and capabilities, visit the AWS X-Ray [Documentation](https://docs.aws.amazon.com/xray/latest/devguide/xray-concepts.html).
{{% /notice %}}

### View Metrics in CloudWatch

CloudWatch Metrics allows you to monitor your instrumented Java application's performance through various OpenTelemetry metrics. These include JVM memory usage, thread counts, and garbage collection statistics, which are essential for monitoring application health and optimizing resource usage.

To examine the thread count metric:

1. Navigate to **All Metrics** from the left sidebar under **Metrics**
2. Select the custom namespace _ECS/AWSOTel/Application_
3. Search for the metric _process.runtime.jvm.threads.count_

### View EMF Logs in CloudWatch Logs

The AWS Distro for OpenTelemetry (ADOT) configuration uses the Embedded Metric Format (EMF) as an exporter, which automatically converts your application metrics into CloudWatch metrics. These measurements are also available as logs in CloudWatch Logs, providing additional context for your application's performance.

To examine the structure of an EMF log entry:

1. Navigate to **Log groups** from the left sidebar under **Logs**
2. Find the Log Group _/aws/ecs/application/metrics_
3. Click on the Log Group to explore the log streams

### Search for Specific Log Entries

To find log entries related to JVM thread metrics:

1. Select Logs Insights from the left sidebar under Logs
2. Choose the log group /aws/ecs/application/metrics
3. Enter and run the following query to filter for JVM thread metrics:

```
fields @timestamp, @message, @logStream, @log
| filter @message like /jvm\.threads/
| sort @timestamp desc
| limit 10
```

### Understanding EMF Log Metrics

The query results will show metrics in log format, including dimensions and AWS event information. Key components include:

- **Namespace:** A string representing the CloudWatch namespace for the metric
- **Dimensions:** An array of _DimensionSet_
- **Metrics:** An array of _MetricDefinition_ objects

{{% notice info %}}
For comprehensive details about the EMF format, consult the [CloudWatch Embedded Metric Format Specification](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Embedded_Metric_Format_Specification.html).
{{% /notice %}}
