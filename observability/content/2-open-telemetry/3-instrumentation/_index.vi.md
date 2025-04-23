+++
title = "Đo đạc"
date = 2020-05-14T00:38:32+07:00
weight = 2
chapter = false
pre = "<b>2.3 </b>"
+++

![Instrumentation](/images/1/Instrumentation.png?width=90pc)

### Triển khai Tự động Đo đạc (Injecting Auto-instrumentation)

AWS Distro for OpenTelemetry (ADOT) cung cấp khả năng tự động đo đạc cho nhiều ngôn ngữ lập trình, bao gồm các dịch vụ .NET, Java, Node.js, Python và Go đang chạy trên Amazon ECS. Với ADOT, bạn có thể tạo dấu vết và số liệu mà không cần sửa đổi mã ứng dụng của mình.

Khi bạn tích hợp ADOT với các task ECS của mình, bạn có được khả năng thu thập metadata từ các tài nguyên và dịch vụ được quản lý của AWS. Việc tích hợp này cho phép bạn tương quan dữ liệu hiệu suất ứng dụng với các số liệu cơ sở hạ tầng, giúp giảm thời gian trung bình để giải quyết vấn đề.

### Tự động Đo đạc Ứng dụng UI Java

Trong phần này, chúng ta sẽ trình diễn khả năng tự động đo đạc bằng cách tập trung vào ứng dụng UI dựa trên Java của chúng ta với OpenTelemetry agent. Mặc dù các thành phần khác trong ứng dụng của chúng ta - catalog (viết bằng Golang), cart (Java) và checkout (Node.js) - cũng có thể được tự động đo đạc, chúng ta sẽ sử dụng ví dụ duy nhất này để minh họa các nguyên tắc của OpenTelemetry và tự động đo đạc.

Để bật OpenTelemetry agent cho một ứng dụng Java, bạn cần chỉ định flag --javaagent khi khởi động hoặc đặt nó làm một biến môi trường. Ngoài ra, hầu hết các hệ thống theo dõi (tracing) đều yêu cầu một tên dịch vụ để xác định ứng dụng của bạn. Bạn có thể xác định điều này bằng cách sử dụng biến môi trường OTEL_RESOURCE_ATTRIBUTES với khóa thuộc tính service.name .

### Cấu hình OpenTelemetry Java Agent

Bảng sau đây phác thảo các biến môi trường chính được sử dụng để cấu hình OpenTelemetry Java agent:

| **Biến Môi Trường**         | **Mô Tả**                                                                                                                           |
| --------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| JAVA_TOOL_OPTIONS           | Chỉ định đường dẫn đến Java agent. Điều này hướng dẫn JVM tải OpenTelemetry agent khi khởi động ứng dụng.                           |
| OTEL_SERVICE_NAME           | Đặt tên dịch vụ để xác định ứng dụng của bạn. Tên này được sử dụng để liên kết các dấu vết của ứng dụng với dịch vụ của nó.         |
| OTEL_EXPORTER_OTLP_INSECURE | Cấu hình agent sử dụng kết nối không an toàn (ví dụ: không có TLS). Điều này được sử dụng để giao tiếp với OpenTelemetry Collector. |
| OTEL_JAVAAGENT_ENABLED      | OpenTelemetry Java agent. Biến này phải được đặt thành true để kích hoạt agent cho việc thu thập dấu vết và số liệu.                |

Để triển khai ADOT collector dưới dạng sidecar, chúng ta sẽ tạo một revision mới của định nghĩa task và cập nhật service để sử dụng phiên bản mới nhất này.

Đầu tiên, chúng ta sẽ đăng ký định nghĩa task mới:

```
aws ecs register-task-definition --cli-input-json "$(echo '{
    "family": "retail-store-ecs-ui",
    "executionRoleArn": "arn:aws:iam::'"$ACCOUNT_ID"':role/retailStoreEcsTaskExecutionRole",
    "taskRoleArn": "arn:aws:iam::'"$ACCOUNT_ID"':role/retailStoreEcsTaskRole",
    "networkMode": "awsvpc",
    "requiresCompatibilities": ["FARGATE"],
    "cpu": "1024",
    "memory": "2048",
    "runtimePlatform": {
        "cpuArchitecture": "X86_64",
        "operatingSystemFamily": "LINUX"
    },
    "containerDefinitions": [
        {
            "name": "application",
            "image": "public.ecr.aws/aws-containers/retail-store-sample-ui:0.7.0",
            "portMappings": [
                {
                    "name": "application",
                    "containerPort": 8080,
                    "hostPort": 8080,
                    "protocol": "tcp",
                    "appProtocol": "http"
                }
            ],
            "essential": true,
            "linuxParameters": {
                "initProcessEnabled": true
            },
            "environment": [
                {
                    "name": "JAVA_TOOL_OPTIONS",
                    "value": "-javaagent:/opt/aws-opentelemetry-agent.jar"
                },
                {
                    "name": "OTEL_JAVAAGENT_ENABLED",
                    "value": "true"
                },
                {
                    "name": "OTEL_EXPORTER_OTLP_ENDPOINT",
                    "value": "http://localhost:4317"
                },
                {
                    "name": "OTEL_EXPORTER_OTLP_INSECURE",
                    "value": "true"
                },
                {
                    "name": "OTEL_SERVICE_NAME",
                    "value": "ui-application"
                },
                {
                    "name": "OTEL_TRACES_EXPORTER",
                    "value": "otlp"
                },
                {
                    "name": "OTEL_METRICS_EXPORTER",
                    "value": "otlp"
                },
                {
                    "name": "OTEL_LOGS_EXPORTER",
                    "value": "none"
                },
                {
                    "name": "ENDPOINTS_CATALOG",
                    "value": "http://catalog"
                },
                {
                    "name": "ENDPOINTS_ASSETS",
                    "value": "http://assets"
                }
            ],
            "healthCheck": {
                "command": [
                    "CMD-SHELL",
                    "curl -f http://localhost:8080/actuator/health || exit 1"
                ],
                "interval": 10,
                "timeout": 5,
                "retries": 3,
                "startPeriod": 60
            },
            "logConfiguration": {
                "logDriver": "awslogs",
                "options": {
                    "awslogs-group": "retail-store-ecs-tasks",
                    "awslogs-region": "'"$AWS_REGION"'",
                    "awslogs-stream-prefix": "ui-service"
                }
            }
        },
        {
            "name": "aws-otel-collector",
            "image": "public.ecr.aws/aws-observability/aws-otel-collector:latest",
            "essential": true,
            "portMappings": [
                {
                    "containerPort": 4317,
                    "hostPort": 4317,
                    "protocol": "tcp"
                }
            ],
            "command": [
                "--config=/etc/ecs/ecs-cloudwatch-xray.yaml"
            ],
            "logConfiguration": {
                "logDriver": "awslogs",
                "options": {
                    "awslogs-group": "retail-store-ecs-tasks",
                    "awslogs-region": "'"$AWS_REGION"'",
                    "awslogs-stream-prefix": "aws-otel-collector",
                    "awslogs-create-group": "True"
                }
            }
        }
    ]
}')"
```

![image](/images/4/image.png?width=90pc)

Tiếp theo, chúng ta sẽ cập nhật ECS service cho dịch vụ ui để sử dụng revision định nghĩa task mới (mất khoảng 5 phút):

```
aws ecs update-service --cluster retail-store-ecs-cluster --service ui --task-definition retail-store-ecs-ui
aws ecs wait services-stable --cluster retail-store-ecs-cluster --services ui
```

Sau khi cập nhật service, các task hiện bao gồm sidecar aws-otel-collector:

![image](/images/4/image-1.png?width=90pc)
