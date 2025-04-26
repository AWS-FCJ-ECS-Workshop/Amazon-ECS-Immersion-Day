---
title: "Spot Instances"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: "<b> 1. </b>"
---

{{% notice info %}}
Bạn phải hoàn thành các chương sau như điều kiện tiên quyết cho bài tập này: [Fundamentals](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/fundamentals/)
{{% /notice %}}

Với Fargate Spot, bạn có thể chạy các tác vụ Amazon ECS chịu được gián đoạn với [giá giảm 70%](https://aws.amazon.com/fargate/pricing/) so với giá Fargate theo yêu cầu. Fargate Spot chạy các tác vụ trên năng lực tính toán dư. Khi AWS cần lấy lại năng lực, các tác vụ của bạn sẽ bị gián đoạn với cảnh báo hai phút.

### Capacity Providers

Amazon ECS capacity providers quản lý quy mô cơ sở hạ tầng cho các tác vụ trong các cụm của bạn. Mỗi cụm có thể có một hoặc nhiều capacity provider và một chiến lược capacity provider tùy chọn. Chiến lược capacity provider quyết định cách phân phối các tác vụ trên các capacity provider của cụm. Khi bạn chạy một tác vụ độc lập hoặc tạo một dịch vụ, bạn sử dụng chiến lược capacity provider mặc định của cụm hoặc một chiến lược capacity provider mà thay thế chiến lược mặc định.

### Sửa đổi Cụm để Kích hoạt Capacity Provider Spot

Mặc định, Fargate sử dụng capacity provider Fargate, dựa trên các instances theo yêu cầu:

```bash
aws ecs describe-clusters --clusters retail-store-ecs-cluster
```

```json
{
    "clusters": [
        {
            "clusterArn": "arn:aws:ecs:us-west-2:111111111111:cluster/retail-store-ecs-cluster",
            "clusterName": "retail-store-ecs-cluster",
            "status": "ACTIVE",
            "registeredContainerInstancesCount": 0,
            "runningTasksCount": 2,
            "pendingTasksCount": 0,
            "activeServicesCount": 1,
            "statistics": [],
            "tags": [],
            "settings": [],
            "capacityProviders": [],
            "defaultCapacityProviderStrategy": []
        }
    ],
    "failures": []
}
```

Chúng ta sẽ sửa đổi cụm hiện tại để thêm capacity provider `FARGATE_SPOT` và xác định chiến lược của chúng ta:

```bash
aws ecs put-cluster-capacity-providers \
    --cluster retail-store-ecs-cluster \
    --capacity-providers FARGATE FARGATE_SPOT \
    --default-capacity-provider-strategy \
        capacityProvider=FARGATE,weight=1,base=1 \
        capacityProvider=FARGATE_SPOT,weight=4
```

```json
{
    "cluster": {
        "clusterArn": "arn:aws:ecs:us-west-2:111111111111:cluster/retail-store-ecs-cluster",
        "clusterName": "retail-store-ecs-cluster",
        "status": "ACTIVE",
        "registeredContainerInstancesCount": 0,
        "runningTasksCount": 0,
        "pendingTasksCount": 0,
        "activeServicesCount": 0,
        "statistics": [],
        "tags": [],
        "settings": [
            {
                "name": "containerInsights",
                "value": "enabled"
            }
        ],
        "capacityProviders": [
            "FARGATE",
            "FARGATE_SPOT"
        ],
        "defaultCapacityProviderStrategy": [
            {
                "capacityProvider": "FARGATE",
                "weight": 1,
                "base": 0
            },
            {
                "capacityProvider": "FARGATE_SPOT",
                "weight": 4,
                "base": 1
            }
        ],
        "attachments": [],
        "attachmentsStatus": "UPDATE_IN_PROGRESS"
    }
}
```

Lệnh trên với cờ `--default-capacity-provider-strategy` có nghĩa là nếu không có capacity provider hoặc loại khởi tạo nào được chỉ định trong quá trình tạo dịch vụ hoặc chạy tác vụ, chiến lược capacity provider mặc định này cho cụm ECS sẽ được sử dụng.

- `FARGATE` thường có base là 0 để đảm bảo các tác vụ không sử dụng các instances theo yêu cầu.
- `FARGATE_SPOT` có trọng số cao hơn (4:1), có nghĩa là nhiều tác vụ sẽ ưu tiên sử dụng Spot khi có thể.
- Giá trị `base` chỉ định số lượng tối thiểu các tác vụ chạy trên capacity provider đã chỉ định. Chỉ có một capacity provider trong một chiến lược capacity provider có thể có base được định nghĩa.
- Giá trị `weight` chỉ định tỷ lệ phần trăm tương đối của tổng số tác vụ được khởi tạo nên nên sử dụng capacity provider đã chỉ định. Ví dụ, nếu bạn có một chiến lược chứa hai capacity provider, và cả hai có trọng số là 1, thì khi base được đáp ứng, các tác vụ sẽ được chia đều giữa hai capacity provider.

### Sửa đổi ứng dụng Retail Store

Dịch vụ hiện chưa sử dụng Spot. Không có capacity provider cụ thể nào trong dịch vụ, vì vậy, mặc định `FARGATE` sẽ được sử dụng.

Hãy sửa đổi dịch vụ `ui` hiện tại để tận dụng các instances Spot và buộc một bản triển khai mới:

```bash
aws ecs update-service \
    --cluster retail-store-ecs-cluster \
    --service ui \
    --capacity-provider-strategy \
        capacityProvider=FARGATE,weight=1,base=0 \
        capacityProvider=FARGATE_SPOT,weight=4,base=1 \
    --force-new-deployment
```

```json
{
    "service": {
        "serviceArn": "arn:aws:ecs:us-west-2:111111111111:service/retail-store-ecs-cluster/ui",
        "serviceName": "ui",
        "clusterArn": "arn:aws:ecs:us-west-2:111111111111:cluster/retail-store-ecs-cluster",
        "loadBalancers": [],
        "serviceRegistries": [],
        "status": "ACTIVE",
        "desiredCount": 1,
        "runningCount": 1,
        "pendingCount": 0,
        "capacityProviderStrategy": [
            {
                "capacityProvider": "FARGATE",
                "weight": 1,
                "base": 0
            },
            {
                "capacityProvider": "FARGATE_SPOT",
                "weight": 4,
                "base": 1
            }
        ],
        "platformVersion": "LATEST",
        "platformFamily": "Linux",
        "[...]": "..."
    }
}
```

Lưu ý rằng khi chuyển từ loại khởi tạo sang chiến lược capacity provider trên một dịch vụ hiện có, hoặc thay đổi chiến lược capacity provider trên một dịch vụ đã sử dụng một chiến lược, bạn phải buộc một bản triển khai mới.

Sau khi bản triển khai dịch vụ hoàn tất, bạn có thể xác minh sự thay đổi bằng cách sử dụng lệnh này để lấy tên capacity provider của các tác vụ `ui`:

```bash
tasks_arns=$(aws ecs list-tasks --cluster retail-store-ecs-cluster --service-name ui --query 'taskArns' --output text)
echo ${tasks_arns} | xargs aws ecs describe-tasks --cluster retail-store-ecs-cluster \
    --tasks \
    --query 'tasks[].{lastStatus: lastStatus, startedAt: startedAt, status: healthStatus, capacityProvider: capacityProviderName}' \
    --output table
```

Bạn sẽ thấy `FARGATE_SPOT` trong cột `capacityProvider`:

```plaintext
+--------------------+-----------+---------------------------------------+-----------+
| capacityProvider   | lastStatus|            startedAt                  |  status   |
+--------------------+-----------+---------------------------------------+-----------+
| FARGATE_SPOT       | RUNNING   | 1970-01-01T00:01:44.181000+01:00      | HEALTHY   |
| FARGATE_SPOT       | RUNNING   | 1970-01-01T00:00:04.624000+01:00      | HEALTHY   |
+--------------------+-----------+---------------------------------------+-----------+
```

### Cách xử lý tín hiệu Terminate

Thông thường, container của bạn sẽ nhận được tín hiệu [`SIGTERM`](https://en.wikipedia.org/wiki/Signal_(IPC)#SIGTERM) (15, tín hiệu kết thúc phần mềm). Hầu hết các framework, ứng dụng và máy chủ web đều nhận được tín hiệu này và dừng một cách tốt.

Nếu container không dừng trước thời gian `stopTimeout`, ECS sẽ gửi tín hiệu [`SIGKILL`](https://en.wikipedia.org/wiki/Signal_(IPC)#SIGKILL) (9, không thể bắt được, không thể bỏ qua) và có thể dẫn đến mất dữ liệu hoặc bị hỏng. Lưu ý rằng giá trị `stopTimeout` không thể dài hơn [120 giây](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_definition_parameters.html#container_definition_timeout).

Để xác minh, bạn có thể dừng một trong các tác vụ của mình trong một môi trường không sản xuất và xác minh nó dừng trước giá trị `stopTimeout` và kiểm tra thời gian khác biệt giữa việc dừng và thời điểm container thực sự bị dừng trong log:

```bash
task_arn=$(aws ecs list-tasks --cluster retail-store-ecs-cluster --service-name ui --query 'taskArns[0]' --output text) # lấy chỉ tác vụ đầu tiên
aws ecs stop-task --cluster retail-store-ecs-cluster --task ${task_arn} --reason "Test from ecs-immersion-day"
```

Ví dụ trong ứng dụng của chúng tôi (Spring Boot), nó sẽ in các thông điệp sau cho thấy `SIGTERM` đã được nhận (khi biến môi trường `LOGGING_LEVEL_ROOT` được đặt thành `DEBUG`):

```plaintext
[...]
1970-01-01T00:00:27.762Z DEBUG 7 --- [ionShutdownHook] o.s.b.a.ApplicationAvailabilityBean : Application availability state ReadinessState changed from ACCEPTING_TRAFFIC to REFUSING_TRAFFIC
1970-01-01T00:00:27.778Z DEBUG 7 --- [ionShutdownHook] o.s.c.s.DefaultLifecycleProcessor : Bean 'webServerGracefulShutdown' completed its stop procedure
[...]
```

Thông báo kết thúc của Fargate Spot được gửi đến Amazon EventBridge. Để biết thêm thông tin, bạn có thể đọc [tài liệu](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/fargate-capacity-providers.html#fargate-capacity-providers-termination).