---
title: "Amazon ECS Cluster"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: "<b> 3.1. </b>"
---

### Xây dựng Cluster Amazon ECS

Trong bài thực hành này, bạn sẽ tạo các thành phần cốt lõi của Amazon ECS, bao gồm ECS Cluster, Task Definition và ECS Service. Mục tiêu cuối cùng là triển khai một container được đặt phía sau Application Load Balancer (ALB).

#### Tạo Amazon ECS Cluster

Sử dụng terminal trong VS Code để tạo một Amazon ECS Cluster có tên `retail-store-ecs-cluster` với tính năng CloudWatch Container Insights được kích hoạt. Container Insights thu thập, tổng hợp và tóm tắt các chỉ số (metrics) cùng nhật ký (logs) từ các ứng dụng và microservices được container hóa của bạn.

```bash
export AWS_REGION={your_region} # Replace your working region

aws ecs create-cluster --cluster-name retail-store-ecs-cluster --region $AWS_REGION --settings name=containerInsights,value=enabled
```

Sau khi thực thi lệnh, bạn sẽ nhận được kết quả như sau, trong đó cho thấy CloudWatch Container Insights đã được kích hoạt:

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
                "name": "containerInsights", // containerInsights enabled
                "value": "enabled"
            }
        ],
        "capacityProviders": [],
        "defaultCapacityProviderStrategy": []
    }
}
```

### Kiểm tra Cluster

Để xem chi tiết về cluster bạn vừa tạo:

1. Truy cập vào [Amazon ECS Console](https://console.aws.amazon.com/ecs/)
2. Tìm và chọn cluster có tên `retail-store-ecs-cluster`

![alt text](/images/3-fundamentals/1-ecs-cluster/image-1.png)

Sau khi hoàn thành việc tạo cluster, hãy tiếp tục bước tiếp theo để tạo ECS Task Definition - định nghĩa cách triển khai dịch vụ UI.