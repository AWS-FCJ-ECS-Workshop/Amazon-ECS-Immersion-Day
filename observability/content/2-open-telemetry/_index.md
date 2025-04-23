+++
title = "OpenTelemetry"
date = 2020-05-14T00:38:32+07:00
weight = 2
chapter = false
pre = "<b>2. </b>"
+++

{{% notice info %}}
**Prerequisites:** Complete the [Fundamentals](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/fundamentals/) & [ECS Service Connect](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/networking/) chapter before starting this lab.
{{% /notice %}}

### What is OpenTelemtry?

OpenTelemetry is a comprehensive collection of APIs, SDKs, and tools designed to instrument, generate, collect, and export telemetry data (metrics, logs, and traces). This data helps you analyze your software's performance and behavior in detail. By providing a standardized approach to collecting and transmitting data across various services and applications, OpenTelemetry enables deeper insights into your systems' operations.

### AWS Distro for OpenTelemtry

AWS Distro for OpenTelemetry (ADOT) is a secure, production-ready, AWS-supported distribution of the Cloud Native Computing Foundation (CNCF) OpenTelemetry project.

ADOT allows you to instrument your applications just once while sending correlated logs, metrics, and traces to multiple observability backends. These backends can include:

- Amazon Managed Service for Prometheus
- Amazon CloudWatch
- AWS X-Ray
- Amazon OpenSearch
- Any OpenTelemetry Protocol (OTLP) compliant backend
- Amazon Managed Streaming for Apache Kafka (MSK)

![adot](/images/1/adot.png?width=90pc)

In this tutorial, you will learn how to implement OpenTelemetry in your AWS environment through the following steps:

- Deploying the OpenTelemetry Collector (ADOT Collector) as a sidecar container on Amazon ECS with Fargate
- Instrumenting application code to collect traces and logs using OpenTelemetry
- Analyzing the collected traces and logs in the AWS console
