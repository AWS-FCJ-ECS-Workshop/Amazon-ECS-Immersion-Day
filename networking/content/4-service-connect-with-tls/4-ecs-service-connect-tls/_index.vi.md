---
title: "Xác minh TLS của ECS Service Connect"
date: "`r Sys.Date()`"
weight: 4
chapter: false
pre: "<b> 4.4 </b>"
---

Sau khi hoàn tất thiết lập ECS Service Connect, hãy xác minh rằng TLS đã được bật đúng cách. Phần này hướng dẫn bạn quy trình xác minh thông qua các bước sau:

1. Lấy địa chỉ IP private của task
2. Kết nối tới task có khả năng thực thi lệnh
3. Cài đặt OpenSSL để xác minh chứng chỉ
4. Xác minh cấu hình chứng chỉ TLS

#### Lấy địa chỉ IP Private của Task

{{% notice note %}}
Truy vấn tên DNS trực tiếp sẽ không hiển thị thông tin chứng chỉ.
{{% /notice %}}

Thực thi các lệnh sau để lấy và lưu địa chỉ IP private của task **UI-TLS** đang chạy:

```bash
task_arn=$(aws ecs list-tasks --cluster retail-store-ecs-cluster \
  --service-name ui-tls \
  --query 'taskArns[0]' --output text)

UI_TLS_PRIVATE_IP=$(aws ecs describe-tasks --cluster retail-store-ecs-cluster --tasks $task_arn \
  | jq -r '.tasks[].containers[] | select(.name == "application") | .networkInterfaces[0].privateIpv4Address')
```

Xác nhận địa chỉ IP:

```bash
echo ${UI_TLS_PRIVATE_IP}
```

#### Kết nối tới Task có khả năng Execute Command

Chọn một task **UI** có khả năng thực thi lệnh:

```bash
export ECS_EXEC_TASK_ARN=$(aws ecs list-tasks --cluster retail-store-ecs-cluster \
  --service-name ui --query 'taskArns[0]' --output text | \
  xargs -n1 aws ecs describe-tasks --cluster retail-store-ecs-cluster --tasks | \
  jq -r '.tasks[] | select(.enableExecuteCommand == true) | .taskArn' | \
  head -n 1)

echo ${ECS_EXEC_TASK_ARN}
```

Chuyển biến địa chỉ IP vào task:

```bash
aws ecs execute-command --cluster retail-store-ecs-cluster \
  --task $ECS_EXEC_TASK_ARN \
  --container application \
  --interactive \
  --command "/bin/bash -c 'echo UI_TLS_PRIVATE_IP=$(echo $UI_TLS_PRIVATE_IP) > /app/private_ip.env'"
```

Khởi tạo phiên shell tương tác:

```bash
aws ecs execute-command --cluster retail-store-ecs-cluster \
  --task $ECS_EXEC_TASK_ARN \
  --container application \
  --interactive \
  --command "/bin/bash"
```

#### Cấu hình môi trường

Import biến môi trường:

```bash
source /app/private_ip.env
```

Cài đặt OpenSSL:

```bash
yum install openssl -y
```

#### Xác minh chứng chỉ TLS

Kiểm tra chi tiết chứng chỉ:

```bash
openssl s_client -connect $UI_TLS_PRIVATE_IP:8080 < /dev/null 2> /dev/null \
  | openssl x509 -noout -text
```

Kết quả đầu ra sẽ hiển thị chi tiết chứng chỉ bao gồm:
- Phiên bản và số serial
- Thuật toán chữ ký
- Thông tin người cấp
- Thời hạn hiệu lực
- Chi tiết chủ thể
- Thông tin khóa công khai  
- Phần mở rộng X.509v3
- Chữ ký

Để thoát phiên:

```bash
exit
```