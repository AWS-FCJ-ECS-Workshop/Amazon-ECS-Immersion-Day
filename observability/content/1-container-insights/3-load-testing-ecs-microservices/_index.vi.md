+++
title = "Kiểm thử tải các Microservice trên ECS"
date = 2020-05-14T00:38:32+07:00
weight = 1
chapter = false
pre = "<b>1.3 </b>"
+++

Trong phần này, chúng ta sẽ tạo tải giả lập bằng lệnh _stress_ command, một công cụ dòng lệnh dành cho các hệ điều hành dựa trên Linux để tạo tải cho hệ thống. Chúng ta sẽ _exec_ vào task _ui_ và chạy lệnh _stress_ để kích hoạt việc mở rộng (scaling). Sau đó, chúng ta sẽ quan sát các số liệu CloudWatch để thấy hiệu quả của việc mở rộng.

### Kết nối đến ECS Task

Chạy lệnh sau để chọn một trong các task đang chạy có bật enableExecuteCommand enabled:

```
ECS_EXEC_TASK_ARN=$(aws ecs list-tasks --cluster retail-store-ecs-cluster \
    --service-name ui --query 'taskArns[]' --output text | \
    xargs -n1 aws ecs describe-tasks --cluster retail-store-ecs-cluster --tasks | \
    jq -r '.tasks[] | select(.enableExecuteCommand == true) | .taskArn' | \
    head -n 1)

echo $ECS_EXEC_TASK_ARN
```

Lệnh này sẽ xuất ra ARN của task:

`arn:aws:ecs:us-west-2:XXXXXXXXXX:task/retail-store-ecs-cluster/0564778486a846599b8bd6b544e5f6eb`

Bắt đầu phiên tương tác /bin/bash trong task đang chạy.

```
if [ -z ${ECS_EXEC_TASK_ARN} ]; then echo "ECS_EXEC_TASK_ARN is not correctly configured!"; else
aws ecs execute-command --cluster retail-store-ecs-cluster \
    --task $ECS_EXEC_TASK_ARN \
    --container application \
    --interactive \
    --command "/bin/bash"
fi
```

### Mở rộng (Scaling Up)

Đầu tiên, cài đặt lệnh stress:

`dnf install stress -y`

Bây giờ, hãy chạy lệnh sau để tạo tải cho 10 lõi CPU với thời gian chờ là 180 giây:

`stress -c 10 --timeout 180`

Điều hướng đến trang điều khiển Container Insights và chọn khoảng thời gian là 5 phút như hình bên dưới:

{{% notice note %}}
Sẽ mất vài phút để các số liệu xuất hiện trong CloudWatch Insights. Để quan sát đỉnh tải (stress spike) theo thời gian thực, hãy bật tự động làm mới (auto-refresh) trong 10 giây trên bảng điều khiển CloudWatch.
{{% /notice %}}

![image-5](/images/2/image-5.png?width=90pc)

Trên trang điều khiển Container Insights, bạn có thể quan sát thấy mức sử dụng CPU tăng lên khi tải trên dịch vụ _ui_ tăng lên.

![image-6](/images/2/image-6.png?width=90pc)

Sau khi quan sát hiệu quả của việc mở rộng, hãy kết thúc phiên của bạn:

`exit`
