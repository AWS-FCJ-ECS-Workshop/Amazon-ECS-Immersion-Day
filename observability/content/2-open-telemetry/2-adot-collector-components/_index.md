+++
title = "ADOT Collector Components"
date = 2020-05-14T00:38:32+07:00
weight = 2
chapter = false
pre = "<b>2.2 </b>"
+++

In this workshop, we will deploy the AWS Distro for OpenTelemetry Collector (ADOT Collector) as a sidecar container in Amazon ECS. Our retail applications, running within the same Amazon ECS task, will be configured to send monitoring data to the ADOT Collector.

### Policies Required for ADOT Collector

To operate the ADOT Collector on Amazon ECS effectively, specific IAM permissions must be attached to your ECS task role. These permissions enable the ADOT Collector to collect and transmit logs, traces, and metrics to various AWS services including CloudWatch, X-Ray, and others.

Let's begin by creating the IAM policy for the OpenTelemetry Collector. This policy grants the necessary permissions for comprehensive telemetry collection.

```
aws iam create-policy \
    --policy-name AWSOpenTelemetryPolicy \
    --policy-document '{
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": [
                    "logs:PutLogEvents",
                    "logs:CreateLogGroup",
                    "logs:CreateLogStream",
                    "logs:DescribeLogStreams",
                    "logs:DescribeLogGroups",
                    "logs:PutRetentionPolicy",
                    "xray:PutTraceSegments",
                    "xray:PutTelemetryRecords",
                    "xray:GetSamplingRules",
                    "xray:GetSamplingTargets",
                    "xray:GetSamplingStatisticSummaries",
                    "cloudwatch:PutMetricData",
                    "ec2:DescribeVolumes",
                    "ec2:DescribeTags",
                    "ssm:GetParameters"
                ],
                "Resource": "*"
            }
        ]
    }'

aws iam attach-role-policy \
    --role-name retailStoreEcsTaskRole \
    --policy-arn arn:aws:iam::$ACCOUNT_ID:policy/AWSOpenTelemetryPolicy
```

![image](/images/3/image.png?width=90pc)
![image-1](/images/3/image-1.png?width=90pc)

### Understanding the Default ADOT Configuration

The ADOT Collector configuration specifies how telemetry data (logs, traces, and metrics) is processed and exported. AWS provides a default configuration optimized for Amazon ECS environments. You can examine the complete default configuration [here](https://github.com/aws-observability/aws-otel-collector/blob/main/config/ecs/ecs-cloudwatch-xray.yaml).

### Configuration Breakdown

**Extensions:** The health_check extension enables monitoring of the collector's operational status.

```
extensions:
  health_check:
```

**Receivers:** The otlp receiver is configured to accept telemetry data through both gRPC and HTTP protocols on designated endpoints.

```
receivers:
  otlp:
    protocols:
      grpc:
        endpoint: 0.0.0.0:4317
      http:
        endpoint: 0.0.0.0:4318
```

**Processors:** The configuration includes several processors:

- _batch/traces_ and _batch/metrics_ handle the batching of trace and metric data
- _resourcedetection_ automatically identifies resource attributes from environment, ECS, and EC2 sources
- _resource_ manages resource attributes through setting and removal operations

```
processors:
  batch/traces:
    timeout: 1s
    send_batch_size: 50
  batch/metrics:
    timeout: 60s
  resourcedetection:
    detectors:
      - env
      - ecs
      - ec2
  resource:
    attributes:
      - key: TaskDefinitionFamily
        from_attribute: aws.ecs.task.family
        action: insert
      - key: aws.ecs.task.family
        action: delete
      - key: InstanceId
        from_attribute: host.id
        action: insert
      - key: host.id
        action: delete
      - key: TaskARN
        from_attribute: aws.ecs.task.arn
        action: insert
      - key: aws.ecs.task.arn
        action: delete
      - key: TaskDefinitionRevision
        from_attribute: aws.ecs.task.revision
        action: insert
      - key: aws.ecs.task.revision
        action: delete
      - key: LaunchType
        from_attribute: aws.ecs.launchtype
        action: insert
      - key: aws.ecs.launchtype
        action: delete
      - key: ClusterARN
        from_attribute: aws.ecs.cluster.arn
        action: insert
      - key: aws.ecs.cluster.arn
        action: delete
      - key: cloud.provider
        action: delete
      - key: cloud.platform
        action: delete
      - key: cloud.account.id
        action: delete
      - key: cloud.region
        action: delete
      - key: cloud.availability_zone
        action: delete
      - key: aws.log.group.names
        action: delete
      - key: aws.log.group.arns
        action: delete
      - key: aws.log.stream.names
        action: delete
      - key: host.image.id
        action: delete
      - key: host.name
        action: delete
      - key: host.type
        action: delete
```

**Exporters:** The configuration defines two exporters:

- _awsxray_ for transmitting trace data to AWS X-Ray
- _awsemf/application_ for sending metrics to Amazon CloudWatch with specific log group and namespace settings

```
exporters:
  awsxray:
  awsemf/application:
    namespace: ECS/AWSOTel/Application
    log_group_name: '/aws/ecs/application/metrics'
    dimension_rollup_option: NoDimensionRollup
    resource_to_telemetry_conversion:
      enabled: true
```

**Services:** The configuration establishes two primary pipelines:

A _traces_ pipeline for processing and exporting trace data
A _metrics/application_ pipeline for handling metrics Both pipelines integrate with the health_check extension for monitoring collector health.

```
service:
  pipelines:
    traces:
      receivers: [otlp]
      processors: [resourcedetection, batch/traces]
      exporters: [awsxray]
    metrics/application:
      receivers: [otlp]
      processors: [resourcedetection, resource, batch/metrics]
      exporters: [awsemf/application]
  extensions: [health_check]
```
