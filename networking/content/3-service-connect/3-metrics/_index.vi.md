---
title: "Metrics"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: "<b> 3.3. </b>"
---

Trong phần này, chúng ta sẽ tìm hiểu về các metrics mà Service Connect cung cấp để giám sát tài nguyên của bạn.

Amazon ECS cung cấp CloudWatch metrics để giúp bạn giám sát tài nguyên một cách hiệu quả. Để xem danh sách đầy đủ các metrics có sẵn, tham khảo [Amazon ECS Developer Guide](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/available-metrics.html).

Để tạo lưu lượng giả lập cho việc phân tích của chúng ta, thực thi lệnh sau:

```bash
export RETAIL_ALB=$(aws elbv2 describe-load-balancers --name retail-store-ecs-ui \
  --query 'LoadBalancers[0].DNSName' --output text)

hey -n 1000000 -c 1 -q 10 http://$RETAIL_ALB/home &
```

### Kiểm tra ECS Service Connect Metrics trên Traffic Health Dashboard

1. Điều hướng đến Amazon ECS Console và chọn `retail-store-ecs-cluster`.
2. Chuyển đến tab Service.

![Service List](/images/3-service-connect/3-metrics/image.png)

3. Chọn service `ui` và cuộn xuống dashboard **Traffic Health**.

Amazon ECS truyền metrics đến CloudWatch mỗi phút, thu thập nhiều điểm dữ liệu mỗi phút. Để biết thêm chi tiết, tham khảo [Amazon ECS Developer Guide](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/available-metrics.html).

![Incoming Traffic](/images/3-service-connect/3-metrics/image-1.png)

Lưu ý rằng Amazon ECS chỉ cấu hình tasks và containers để định tuyến kết nối ứng dụng thông qua proxy cho các tên endpoint trong cùng namespace. Tất cả lưu lượng khác sẽ bỏ qua proxy, bao gồm địa chỉ IP trong cùng VPC, endpoints của dịch vụ AWS và lưu lượng bên ngoài. Điều này giải thích việc không có lưu lượng đi ra cho các service `Assets` và `Catalog`. Để biết thêm thông tin, tham khảo [Service Connect Concepts documentation](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/service-connect-concepts-deploy.html#service-connect-concepts-proxy).

### Phân tích ECS Service Connect Metrics trong Amazon CloudWatch

1. Mở [Amazon CloudWatch metrics console](https://console.aws.amazon.com/cloudwatch/home#metricsV2?graph=~(view~'timeSeries~stacked~false~stat~'Average~period~300)&namespace=~'AWS*2fECS).

![ECS CloudWatch Metrics](/images/3-service-connect/3-metrics/image-2.png)

2. Nhấp vào metrics **Cluster, DiscoveryName, ServiceName**.
3. Tìm kiếm `ui` để xem các metrics về lưu lượng.

![Discovery Name Metrics](/images/3-service-connect/3-metrics/image-3.png)

Để có thêm thông tin chi tiết, khám phá metrics **Cluster, ServiceName, TargetDiscoveryName**.

Để dừng việc tạo lưu lượng giả lập, chạy lệnh sau:

```bash
pkill -9 hey
```

Bằng cách phân tích các metrics này, bạn có thể có được những hiểu biết quý giá về hiệu suất và hành vi của các ECS services sử dụng Service Connect.