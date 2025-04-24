---
title: "Kiểm tra các service"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: "<b> 3.2. </b>"
---

Điều hướng đến Amazon ECS console để kiểm tra các service đã triển khai.

[Mở Amazon ECS console](https://console.aws.amazon.com/ecs/v2/clusters/retail-store-ecs-cluster/services)

Cluster chứa ba service đang chạy:

![Danh sách Service trong Cluster](/images/3-service-connect/2-examinate-services/image.png)
*Hình 1. Danh sách Service trong Cluster*

Trong tab **Tasks**, bạn có thể xem tất cả các task đang chạy:

![Danh sách Task trong Cluster](/images/3-service-connect/2-examinate-services/image-1.png)
*Hình 2. Danh sách Task trong Cluster*

Chọn bất kỳ task đang chạy và điều hướng đến phần **Containers**. Mỗi task chứa ba container:
- Container `application`
- Container proxy `ecs-service-connect` cho service discovery và kết nối
- Container `aws-guardduty-agent` cho giám sát bảo mật runtime

Để biết thêm chi tiết về Service Connect proxy, xem [tài liệu về khái niệm Service Connect](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/service-connect-concepts.html#service-connect-concepts-proxy).

![Các container trong Task của Cluster](/images/3-service-connect/2-examinate-services/image-2.png)
*Hình 3. Các container trong Task của Cluster*

#### Namespace

Service Connect sử dụng namespace [AWS Cloud Map](https://aws.amazon.com/cloud-map/) để nhóm các task Amazon ECS cần giao tiếp với nhau một cách logic. Các điểm chính về namespace:

- Mỗi service Amazon ECS chỉ có thể thuộc về một namespace
- Các service trong một namespace có thể trải rộng trên nhiều cluster Amazon ECS trong cùng Region và tài khoản AWS

Để xem Namespace của Cluster:
1. Mở dashboard Amazon ECS
2. Chọn **Namespace** từ menu điều hướng

![Namespace của Cluster](/images/3-service-connect/2-examinate-services/image-3.png)
*Hình 4. Namespace của Cluster*

Chọn một namespace để xem chi tiết cấu hình AWS Cloud Map của nó. Trường **Discovery Name** hiển thị tên ngắn được sử dụng cho kết nối service trong cùng namespace (ví dụ: `http://assets`).

![Namespace của Cluster](/images/3-service-connect/2-examinate-services/image-4.png)
*Hình 5. Các service trong namespace **retailstore.local***