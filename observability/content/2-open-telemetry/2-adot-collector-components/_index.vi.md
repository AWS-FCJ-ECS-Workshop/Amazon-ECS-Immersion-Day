+++
title = "Các thành phần của ADOT Collector"
date = 2020-05-14T00:38:32+07:00
weight = 2
chapter = false
pre = "<b>2.2 </b>"
+++

Trong workshop này, chúng ta sẽ triển khai AWS Distro for OpenTelemetry Collector (ADOT Collector) như một sidecar container trong Amazon ECS. Các ứng dụng bán lẻ của chúng ta, chạy trong cùng một task Amazon ECS, sẽ được cấu hình để gửi dữ liệu giám sát đến ADOT Collector.

### Các Chính sách Cần thiết cho ADOT Collector

Để vận hành ADOT Collector trên Amazon ECS một cách hiệu quả, các quyền IAM cụ thể phải được đính kèm vào vai trò task ECS của bạn. Các quyền này cho phép ADOT Collector thu thập và truyền logs, traces và metrics đến các dịch vụ AWS khác nhau bao gồm CloudWatch, X-Ray và các dịch vụ khác.

Hãy bắt đầu bằng cách tạo chính sách IAM cho OpenTelemetry Collector. Chính sách này cấp các quyền cần thiết cho việc thu thập telemetry toàn diện.

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

### Tìm hiểu về Cấu hình ADOT Mặc định

Cấu hình ADOT Collector chỉ định cách dữ liệu telemetry (logs, traces và metrics) được xử lý và xuất. AWS cung cấp một cấu hình mặc định được tối ưu hóa cho môi trường Amazon ECS. Bạn có thể xem xét cấu hình mặc định hoàn chỉnh [tại đây](https://github.com/aws-observability/aws-otel-collector/blob/main/config/ecs/ecs-cloudwatch-xray.yaml).

### Phân tích Cấu hình

**Extensions:** Extension health_check cho phép giám sát trạng thái hoạt động của collector.

```
extensions:
  health_check:
```

**Receivers:** Receiver otlp được cấu hình để chấp nhận dữ liệu telemetry thông qua cả giao thức gRPC và HTTP trên các endpoint được chỉ định.

```
receivers:
  otlp:
    protocols:
      grpc:
        endpoint: 0.0.0.0:4317
      http:
        endpoint: 0.0.0.0:4318
```

**Processors:** Cấu hình bao gồm một số processor:

- _batch/traces_ và _batch/metrics_ xử lý việc gom nhóm dữ liệu traces và số liệu
- _resourcedetection_ tự động xác định các thuộc tính tài nguyên từ môi trường, ECS và các nguồn EC2
- _resource_ quản lý các thuộc tính tài nguyên thông qua các hoạt động thiết lập và xóa

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

**Exporters:** Cấu hình định nghĩa hai exporter:

- _awsxray_ để truyền dữ liệu traces đến AWS X-Ray
- _awsemf/application_ để gửi số liệu đến Amazon CloudWatch với các cài đặt log group và namespace cụ thể

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

**Services:** Cấu hình thiết lập hai pipeline chính:

Một _traces_ pipeline để xử lý và xuất dữ liệu traces
Một _metrics/application_ để xử lý số liệu Cả hai pipeline đều tích hợp với extension health_check để giám sát trạng thái của collector.

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
