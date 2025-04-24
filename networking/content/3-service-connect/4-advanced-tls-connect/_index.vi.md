---
title: "Kết nối TLS Nâng cao"
date: "`r Sys.Date()`"
weight: 4
chapter: false
pre: "<b> 3.4. </b>"
---

Trong phần này, bạn sẽ khám phá các khả năng của ECS Service Connect bằng cách thiết lập các phiên tương tác với các container đang chạy để hiểu cấu hình và chức năng của Service Connect.

#### Kết nối tới ECS Task

Chạy lệnh sau để xác định một task đang chạy có bật `enableExecuteCommand`:

```bash
ECS_EXEC_TASK_ARN=$(aws ecs list-tasks --cluster retail-store-ecs-cluster \
    --service-name ui --query 'taskArns[]' --output text | \
    xargs -n1 aws ecs describe-tasks --cluster retail-store-ecs-cluster --tasks | \
    jq -r '.tasks[] | select(.enableExecuteCommand == true) | .taskArn' | \
    head -n 1)

echo $ECS_EXEC_TASK_ARN
```

Lệnh sẽ xuất ra task ARN:

```
arn:aws:ecs:us-west-2:XXXXXXXXXX:task/retail-store-ecs-cluster/0564778486a846599b8bd6b544e5f6eb
```

Bắt đầu một phiên `/bin/bash` tương tác trong task đang chạy:

```bash
if [ -z ${ECS_EXEC_TASK_ARN} ]; then echo "ECS_EXEC_TASK_ARN is not correctly configured!"; else
aws ecs execute-command --cluster retail-store-ecs-cluster \
    --task $ECS_EXEC_TASK_ARN \
    --container application \
    --interactive \
    --command "/bin/bash"
fi
```

#### Cấu hình Môi trường

Cài đặt trình xử lý JSON dòng lệnh `jq` để định dạng đầu ra JSON:

```bash
yum install jq -y
```

#### Kiểm tra Cấu hình File Host

Xem file `/etc/hosts` để thấy ánh xạ giữa các tên miền đầy đủ (FQDN) và địa chỉ IP:

```bash
cat /etc/hosts
```

Đầu ra:
```
127.0.0.1 localhost
X.X.X.X ip-X-X-X-X.us-west-2.compute.internal
127.255.0.1 assets
2600:f0f0:0:0:0:0:0:1 assets
127.255.0.2 catalog
2600:f0f0:0:0:0:0:0:2 catalog
127.255.0.3 ui
2600:f0f0:0:0:0:0:0:3 ui
```

File này hiển thị ba tên Discovery được cấu hình trong ECS Namespace, mỗi tên với địa chỉ IPv4 (`127.255.0.X`) và IPv6 riêng biệt. Các địa chỉ này định tuyến đến proxy cục bộ ECS, điều hướng lưu lượng đến proxy từ xa ECS thích hợp cho mỗi dịch vụ.

#### Xác minh Kết nối Dịch vụ Catalog

Kiểm tra kết nối tới Catalog API từ bên trong container:

```bash
curl http://catalog/catalogue/tags | jq
```

Đầu ra mong đợi:
```json
[
  {
    "name": "smart",
    "displayName": "Smart"
  },
  {
    "name": "dress",
    "displayName": "Dress"
  },
  {
    "name": "luxury",
    "displayName": "Luxury"
  },
  {
    "name": "casual",
    "displayName": "Casual"
  }
]
```

Để kết thúc phiên `exec`:
```bash
exit
```