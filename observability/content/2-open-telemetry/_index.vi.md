+++
title = "OpenTelemetry"
date = 2020-05-14T00:38:32+07:00
weight = 2
chapter = false
pre = "<b>2. </b>"
+++

{{% notice info %}}
**Điều kiện tiên quyết:** Hoàn thành chương [Fundamentals](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/fundamentals/) & [ECS Service Connect](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/networking/) trước khi bắt đầu lab này.
{{% /notice %}}

### OpenTelemetry là gì?

OpenTelemetry là một tập hợp toàn diện các API, SDK và công cụ được thiết kế để đo đạc, tạo, thu thập và xuất dữ liệu telemetry (metrics, logs và traces). Dữ liệu này giúp bạn phân tích chi tiết hiệu suất và hành vi của phần mềm. Bằng cách cung cấp một phương pháp tiêu chuẩn để thu thập và truyền dữ liệu trên nhiều dịch vụ và ứng dụng khác nhau, OpenTelemetry cho phép bạn hiểu sâu hơn về hoạt động của hệ thống.

### AWS Distro for OpenTelemetry

AWS Distro for OpenTelemetry (ADOT) là một bản phân phối an toàn, sẵn sàng cho môi trường production và được AWS hỗ trợ của dự án OpenTelemetry thuộc Cloud Native Computing Foundation (CNCF).

ADOT cho phép bạn đo đạc ứng dụng của mình chỉ một lần trong khi gửi các log, metric và trace tương quan đến nhiều backend observability. Các backend này có thể bao gồm:

- Amazon Managed Service for Prometheus
- Amazon CloudWatch
- AWS X-Ray
- Amazon OpenSearch
- Bất kỳ backend tuân thủ OpenTelemetry Protocol (OTLP) nào
- Amazon Managed Streaming for Apache Kafka (MSK)

![adot](/images/1/adot.png?width=90pc)

Trong hướng dẫn này, bạn sẽ học cách triển khai OpenTelemetry trong môi trường AWS của mình thông qua các bước sau:

- Triển khai OpenTelemetry Collector (ADOT Collector) dưới dạng một sidecar container trên Amazon ECS với Fargate
- Đo đạc mã ứng dụng để thu thập traces và logs bằng OpenTelemetry
- Phân tích traces và logs đã thu thập trong bảng điều khiển AWS
