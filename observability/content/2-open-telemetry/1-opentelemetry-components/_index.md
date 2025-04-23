+++
title = " OpenTelemetry Components"
date = 2020-05-14T00:38:32+07:00
weight = 2
chapter = false
pre = "<b>2.1 </b>"
+++

### OpenTelemetry Collector

The OpenTelemetry Collector is a versatile component designed to export telemetry data to multiple destinations, including Prometheus, AWS X-Ray, and Amazon CloudWatch. The AWS Distro for OpenTelemetry Collector represents AWS's supported distribution of the upstream OpenTelemetry Collector. This AWS-maintained component ensures reliable integration with Amazon CloudWatch and various other supported backends, including partner ISV solutions.

{{% notice info %}}
Refer to our [documentation](https://aws-otel.github.io/docs/releases) to learn about different exporters supported in AWS Distro for OpenTelemetry.
{{% /notice %}}

The OpenTelemetry Collector can be deployed using several patterns to match your architectural requirements:

**Sidecar Pattern:** Deploys the collector alongside your application on the same host, enabling local collection and export of telemetry data.

**ECS Service Pattern:** Operates as a centralized collector service, consolidating telemetry data from multiple application nodes. For comprehensive information about deployment patterns and configurations, consult the AWS blog on [deployment patterns](https://aws.amazon.com/blogs/opensource/deployment-patterns-for-the-aws-distro-for-opentelemetry-collector-with-amazon-elastic-container-service/).

### OpenTelemetry Protocol

The OpenTelemetry Protocol (OTLP) serves as a vendor-neutral specification for transmitting traces, metrics, and logs. It enables seamless backend transitions by requiring only configuration adjustments on the collector. OTLP establishes standardized data encoding, transport, and delivery mechanisms, ensuring future compatibility and straightforward integration.

For an in-depth understanding of OpenTelemetry's components, consult the full specification. You can find a comprehensive specification of OpenTelemetry's [components](https://opentelemetry.io/docs/concepts/components/).

### Instrumentation

OpenTelemetry delivers extensive support for generating telemetry data across diverse libraries and frameworks. It enables both auto-instrumentation and manual instrumentation approaches to capture detailed observability data:

**Auto-Instrumentation:** Collects telemetry data automatically, including traces, metrics, and logs, without requiring modifications to your application code. This method provides rapid integration with common libraries and frameworks, enabling observation of HTTP requests and other operations with minimal configuration.

**Manual Instrumentation:** Provides granular control through direct integration of OpenTelemetry SDKs into your codebase. This approach enables creation of custom spans, metrics, and logs, offering the flexibility to monitor specific application behaviors and detailed performance characteristics.

These complementary approaches can be effectively combined to monitor applications across AWS App Runner, AWS Lambda, EC2, ECS, EKS on EC2, AWS Fargate, and hybrid or on-premises environments. While auto-instrumentation delivers comprehensive coverage, manual instrumentation enables collection of specialized telemetry data.

{{% notice info %}}
Refer to the OpenTelemetry [specification](https://opentelemetry.io/docs/specs/otel/) to learn more.
{{% /notice %}}
