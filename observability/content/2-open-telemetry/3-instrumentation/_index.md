+++
title = "Instrumentation"
date = 2020-05-14T00:38:32+07:00
weight = 2
chapter = false
pre = "<b>2.3 </b>"
+++

![Instrumentation](/images/1/Instrumentation.png?width=90pc)

### Injecting Auto-instrumentation

The AWS Distro for OpenTelemetry (ADOT) provides auto-instrumentation capabilities for multiple programming languages, including .NET, Java, Node.js, Python, and Go services running in Amazon ECS. With ADOT, you can generate traces and metrics without modifying your application code.

When you integrate ADOT with your ECS tasks, you gain the ability to collect metadata from your AWS resources and managed services. This integration enables you to correlate application performance data with infrastructure metrics, helping to reduce mean time to problem resolution.

### Auto-instrumenting Java UI Application

In this section, we'll demonstrate auto-instrumentation by focusing on our Java-based UI application with the OpenTelemetry agent. While the other components in our application - catalog (written in Golang), cart (Java), and checkout (Node.js) - can also be auto-instrumented, we'll use this single example to illustrate the principles of OpenTelemetry and auto-instrumentation.

To enable the OpenTelemetry agent for a Java application, you need to specify the --javaagent flag at startup or set it as an environment variable. Additionally, most tracing systems require a service name to identify your application. You can define this using the OTEL_RESOURCE_ATTRIBUTES environment variable with the service.name attribute key.

### OpenTelemetry Java Agent Configuration

The following table outlines the key environment variables used for configuring the OpenTelemetry Java agent:

| **Environment Variable**    | **Description**                                                                                                                          |
| --------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| JAVA_TOOL_OPTIONS           | Specifies the path to the Java agent. This tells the JVM to load the OpenTelemetry agent when starting the application.                  |
| OTEL_SERVICE_NAME           | Sets the service name for identifying your application. This name is used to associate your application's traces with its service.       |
| OTEL_EXPORTER_OTLP_INSECURE | Configures the agent to use an insecure connection (e.g., without TLS). This is used for communication with the OpenTelemetry Collector. |
| OTEL_JAVAAGENT_ENABLED      | Enables the OpenTelemetry Java agent. This variable must be set to true to activate the agent for tracing and metrics collection.        |

To deploy the ADOT collector as a sidecar, we'll create a new revision of the task definition and update the service to use this latest version.

First, we'll register the new task definition:

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

Next, we'll update the ECS service for the ui service to use the new task definition revision (~ 5 min):

```
aws ecs update-service --cluster retail-store-ecs-cluster --service ui --task-definition retail-store-ecs-ui
aws ecs wait services-stable --cluster retail-store-ecs-cluster --services ui
```

After updating the service, the tasks now include the aws-otel-collector sidecar:

![image](/images/4/image-1.png?width=90pc)
