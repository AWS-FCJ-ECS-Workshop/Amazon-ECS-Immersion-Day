---
title: "Metrics"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: "<b> 3.3. </b>"
---

In this section, we'll explore the metrics that Service Connect provides for monitoring your resources.

Amazon ECS offers CloudWatch metrics to help you monitor your resources effectively. For a comprehensive list of available metrics, refer to the [Amazon ECS Developer Guide](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/available-metrics.html).

To generate synthetic traffic for our analysis, execute the following command:

```bash
export RETAIL_ALB=$(aws elbv2 describe-load-balancers --name retail-store-ecs-ui \
  --query 'LoadBalancers[0].DNSName' --output text)

hey -n 1000000 -c 1 -q 10 http://$RETAIL_ALB/home &
```

### Examining ECS Service Connect Metrics on the Traffic Health Dashboard

1. Navigate to the Amazon ECS Console and select the `retail-store-ecs-cluster`.
2. Go to the Service tab.

![Service List](/images/3-service-connect/3-metrics/image.png)

3. Select the `ui` service and scroll down to the **Traffic Health** dashboard.

Amazon ECS transmits metrics to CloudWatch every minute, collecting multiple data points per minute. For more details, consult the [Amazon ECS Developer Guide](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/available-metrics.html).

![Incoming Traffic](/images/3-service-connect/3-metrics/image-1.png)

Note that Amazon ECS configures tasks and containers to route application connections through the proxy only for endpoint names within the same namespace. All other traffic bypasses the proxy, including IP addresses in the same VPC, AWS service endpoints, and external traffic. This explains the absence of outgoing traffic for the `Assets` and `Catalog` services. For more information, refer to the [Service Connect Concepts documentation](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/service-connect-concepts-deploy.html#service-connect-concepts-proxy).

### Analyzing ECS Service Connect Metrics in Amazon CloudWatch

1. Open the [Amazon CloudWatch metrics console](https://console.aws.amazon.com/cloudwatch/home#metricsV2?graph=~(view~'timeSeries~stacked~false~stat~'Average~period~300)&namespace=~'AWS*2fECS).

![ECS CloudWatch Metrics](/images/3-service-connect/3-metrics/image-2.png)

2. Click on the **Cluster, DiscoveryName, ServiceName** metrics.
3. Search for `ui` to review the traffic metrics.

![Discovery Name Metrics](/images/3-service-connect/3-metrics/image-3.png)

For additional insights, explore the **Cluster, ServiceName, TargetDiscoveryName** metrics.

To terminate the synthetic traffic generation, run the following command:

```bash
pkill -9 hey
```

By analyzing these metrics, you can gain valuable insights into the performance and behavior of your ECS services using Service Connect.