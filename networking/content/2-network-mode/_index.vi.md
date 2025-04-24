---
title: "Chế độ mạng Amazon ECS"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: "<b> 2. </b>"
---

{{% notice info %}}
Hoàn thành chương [Fundamentals](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/fundamentals/) trước khi tiếp tục bài lab này.
{{% /notice %}}

Cấu hình mạng container là một khía cạnh quan trọng khi chạy container trên máy chủ. Phần này tập trung vào **chế độ mạng AWSVPC**, điều này là bắt buộc đối với Amazon ECS trên Fargate. Để biết thông tin chi tiết về việc lựa chọn chế độ mạng, tham khảo [tài liệu Amazon ECS](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-networking.html).

Với **chế độ AWSVPC**, Amazon ECS tạo và quản lý một Elastic Network Interface (ENI) cho mỗi task. Mỗi task nhận được một địa chỉ IP riêng trong VPC của bạn, cho phép kiểm soát chi tiết việc giao tiếp của task và service. Chế độ mạng AWSVPC hỗ trợ cả loại khởi chạy Amazon EC2 và Fargate. Tìm hiểu thêm về [mạng AWSVPC](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-networking-awsvpc.html).

{{% notice info %}}
Chế độ mạng AWSVPC là bắt buộc đối với các task Amazon ECS chạy trên Fargate.
{{% /notice %}}

![network mode awsvpc](/images/2-network-mode/image.png)
*Hình 1. Chế độ mạng AWSVPC*

#### Kiểm tra cấu hình mạng trong Amazon ECS Cluster

Điều hướng đến bảng điều khiển Amazon ECS để kiểm tra cấu hình service:

[Amazon ECS Console](https://console.aws.amazon.com/ecs/v2/clusters/retail-store-ecs-cluster/tasks)

![Danh sách Task trong Cluster](/images/2-network-mode/image-1.png)
*Hình 2. Danh sách Task trong Cluster*

Chọn bất kỳ task đang chạy và cuộn xuống phần **Configuration** để xem:
- Chế độ mạng
- ID của ENI
- Địa chỉ IP riêng

![Chi tiết Task trong Cluster](/images/2-network-mode/image-2.png)
*Hình 3. Chi tiết Task trong Cluster*

Để lấy thông tin task theo cách lập trình, thực thi lệnh AWS CLI sau cho service `ui`:

```bash
aws ecs describe-tasks \
  --cluster retail-store-ecs-cluster \
  --tasks $(aws ecs list-tasks --cluster retail-store-ecs-cluster --service ui --query 'taskArns[0]' --output text)
```

Kết quả mẫu:

```json
{
    "tasks": [
        {
            "attachments": [
                {
                    "id": "464044b3-626f-44da-86ec-fa20a064d408",
                    "type": "ElasticNetworkInterface",
                    "status": "ATTACHED",
                    ...
                }
            ],
            ...
            "networkInterfaces": [
                {
                    "attachmentId": "464044b3-626f-44da-86ec-fa20a064d408",
                    "privateIpv4Address": "10.0.4.128"
                }
            ],
            "healthStatus": "HEALTHY",
            "cpu": "0"
        }
    ]
}
```