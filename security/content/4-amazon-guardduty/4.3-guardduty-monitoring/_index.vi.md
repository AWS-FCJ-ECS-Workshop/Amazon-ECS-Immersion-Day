---
title: "GuardDuty Runtime Monitoring"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: "<b> 4.3. </b>"
---

GuardDuty Runtime Monitoring cho phép giám sát bảo mật các task đang chạy trong Amazon ECS clusters trên AWS Fargate thông qua một security agent được quản lý hoàn toàn. Khi được kích hoạt, GuardDuty sẽ tự động triển khai security agent này để giám sát các Fargate task mới trong ECS clusters của bạn.

#### Triển khai Security Agent thông qua Sidecar Containers

Đối với mỗi Fargate task hoặc service mới, GuardDuty tự động gắn một sidecar container vào mọi container trong ECS Fargate task. GuardDuty security agent chạy trong sidecar container này, thu thập các sự kiện runtime từ tất cả các container trong các task này.

{{% notice note %}}
Runtime Monitoring chỉ áp dụng cho các Fargate task mới. Các task đang chạy hiện tại sẽ không nhận được GuardDuty sidecar container do tính chất không thể thay đổi của Fargate tasks.
{{% /notice %}}

Để xác minh việc triển khai GuardDuty agent:

1. Điều hướng đến Amazon ECS console
2. Chọn các task của service `ui`
3. Tìm phần containers
4. Xác nhận sự hiện diện của `aws-guardduty-agent` đang chạy cùng với container `application`

[Truy cập Amazon ECS Console](https://console.aws.amazon.com/ecs/v2/clusters/retail-store-ecs-cluster/services/ui/tasks)

![Guardduty ECS SideCard Container](/images/4-amazon-guardduty/4.3-guardduty-monitoring/image.png)
*Hình 1. Guardduty ECS SideCard Container*

Phần tiếp theo sẽ trình bày khả năng phát hiện của GuardDuty bằng cách triển khai một ECS task thử nghiệm mô phỏng hoạt động trái phép.

Tài nguyên bổ sung:
- [Runtime Monitoring Documentation](https://docs.aws.amazon.com/guardduty/latest/ug/how-runtime-monitoring-works-ecs-fargate.html)
- [Post-Configuration Details](https://docs.aws.amazon.com/guardduty/latest/ug/runtime-monitoring-after-configuration.html)