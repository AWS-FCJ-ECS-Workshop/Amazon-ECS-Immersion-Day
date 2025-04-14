---
title : "Kích hoạt Auto Scaling"
date :  "`r Sys.Date()`" 
weight : 4
chapter : false
pre: "<b> 4. </b>"
---

Phần này trình bày cách tạo tải giả cho UI service để quan sát hành vi Amazon ECS Auto Scaling.

![alt text](/images/4-trigger-auto-scaling/trigger-auto-scaling.png)
*Hình 1. Kích hoạt scaling dựa trên số lượng request của Application Load Balancer*

#### Xác minh DNS Name của Load Balancer

Lấy DNS name của Application Load Balancer được liên kết với UI service. Biến môi trường này phải có sẵn từ thiết lập trước đó.

```bash
export RETAIL_ALB=$(aws elbv2 describe-load-balancers --name retail-store-ecs-ui \
  --query 'LoadBalancers[0].DNSName' --output text)
echo ${RETAIL_ALB}
```

#### Tạo tải

Sử dụng [hey tool](https://github.com/rakyll/hey) để mô phỏng lưu lượng bằng cách gửi request đến endpoint /home của UI service:

```bash
hey -n 1000000 -c 5 -q 40 http://$RETAIL_ALB/home &
```

#### Giám sát hoạt động Scaling

Auto Scaling kích hoạt khi cảnh báo cao cho metric scaling vượt quá ngưỡng trong 3 khoảng thời gian 1 phút liên tiếp. Giám sát trạng thái cảnh báo với lệnh này (khoảng 4 phút):

```bash
sleep 90 && aws cloudwatch wait alarm-exists --alarm-name-prefix \
 TargetTracking-service/retail-store-ecs-cluster/ui-AlarmHigh --state-value ALARM
```

Xem số lượng task của service khi nó scale out từ 2 instances:

```bash
aws ecs describe-tasks \
    --cluster retail-store-ecs-cluster \
    --tasks $(aws ecs list-tasks --cluster retail-store-ecs-cluster --query 'taskArns[]' --output text) \
    --query "tasks[*].[group, launchType, lastStatus, healthStatus, taskArn]" --output table
```

![alt text](/images/4-trigger-auto-scaling/image.png)
*Hình 2. Số lượng task sau thao tác scale-out*

CloudWatch console hiển thị cảnh báo cao chuyển sang trạng thái ALARM:

![alt text](/images/4-trigger-auto-scaling/image-1.png)
*Hình 3. Cảnh báo cao ở trạng thái ALARM*

Giám sát hoạt động scaling trong tab UI Service Events, hiển thị số lượng mong muốn đã tăng:

![alt text](/images/4-trigger-auto-scaling/image-2.png)
*Hình 4. UI Service Events hiển thị hoạt động scaling*

#### Dừng tạo tải

Kết thúc tiến trình hey:

```bash
pkill -9 hey
```

#### Scale Down

Mặc dù service thường tự động scale down sau vài phút, bạn có thể đẩy nhanh quá trình bằng các lệnh sau:

```bash
aws ecs wait services-stable --cluster retail-store-ecs-cluster --services ui

aws ecs update-service \
    --cluster retail-store-ecs-cluster \
    --service ui \
    --task-definition retail-store-ecs-ui \
    --desired-count 2
```

![alt text](/images/4-trigger-auto-scaling/image-3.png)
*Hình 5. Service đã scale down xuống 2 tasks*