+++
title = "Container Insights"
date = 2020-05-14T00:38:32+07:00
weight = 1
chapter = false
pre = "<b>1. </b>"
+++

{{% notice info %}}
**Điều kiện tiên quyết:** Hoàn thành chương [Fundamentals](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/fundamentals/) & [ECS Service Connect](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/networking/) trước khi bắt đầu lab này.
{{% /notice %}}

Theo mặc định, Amazon ECS cung cấp các số liệu cho cluster và service. Tuy nhiên, bằng cách chọn sử dụng Container Insights, bạn có thể nâng cao sự thuận tiện trong vận hành bằng cách truy cập vào các số liệu bổ sung, giúp đơn giản hóa việc quản lý vận hành. [CloudWatch Container Insights](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/cloudwatch-container-insights.html) hu thập, tổng hợp và tóm tắt các số liệu và nhật ký từ các ứng dụng và microservice được container hóa của bạn. Bạn cũng có thể thiết lập cảnh báo CloudWatch dựa trên các số liệu mà Container Insights thu thập.

Trong lab này, trước tiên chúng ta sẽ kiểm tra xem Container Insights đã được bật trên Amazon ECS Cluster của bạn hay chưa. Nếu Container Insights chưa được bật, chúng ta sẽ sử dụng AWS CLI để bật chúng cho CloudWatch.

Kiểm tra trạng thái của Container Insights cho ECS cluster bằng cách lọc đầu ra AWS CLI từ [describe-clusters](https://docs.aws.amazon.com/AmazonECS/latest/APIReference/API_DescribeClusters.html). Đầu ra từ truy vấn AWS CLI sẽ trả về trạng thái Container Insights để cho biết liệu nó đã được bật hay tắt cho ECS Cluster.

```
aws ecs describe-clusters --clusters retail-store-ecs-cluster --include SETTINGS --query 'clusters[*].settings'
```

![image-1](/images/1/image-1.png?width=90pc)

#### Mở rộng nếu Container Insight chưa được bật.

Nếu Container Insights chưa được bật, hãy sử dụng lệnh AWS CLI sau để bật Container Insights cho CloudWatch:

```
aws ecs update-cluster-settings --cluster retail-store-ecs-cluster --settings name=containerInsights,value=enabled
```

Bạn sẽ nhận được đầu ra sau trong dòng lệnh:

```
{
  "cluster": {
    "clusterArn": "arn:aws:ecs:us-west-2:XXXXXXXXXX:cluster/retail-store-ecs-cluster",
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
    "capacityProviders": [],
    "defaultCapacityProviderStrategy": [],
    "attachments": []
  }
}
```
