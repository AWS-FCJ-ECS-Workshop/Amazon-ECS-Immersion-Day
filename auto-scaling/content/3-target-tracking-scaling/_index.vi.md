---
title : "Target Tracking Scaling"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre: "<b> 3. </b>"
---

#### Target Tracking Scaling

![alt text](/images/3-target-tracking-scaling/image-2.png)
*Hình 1: Service scaling*

Cấu hình Amazon ECS Service Auto Scaling sử dụng Target Tracking Scaling để tự động điều chỉnh số lượng task dựa trên nhu cầu ứng dụng.

Đăng ký UI service như một scalable target với Application Auto Scaling bằng cách thiết lập phạm vi scaling từ 2 đến 10 task:

```bash
aws application-autoscaling register-scalable-target \
    --service-namespace ecs \
    --scalable-dimension ecs:service:DesiredCount \
    --resource-id service/retail-store-ecs-cluster/ui \
    --min-capacity 2 \
    --max-capacity 10
```

Tiếp theo, tạo và áp dụng chính sách scaling:

1. Tạo file cấu hình JSON định nghĩa chính sách scaling. Chính sách này sử dụng metric số lượng request Application Load Balancer trên mỗi target, với mục tiêu 1.500 request trên mỗi ECS task:

{{% notice note %}}
Các ngưỡng scaling được hiển thị chỉ mang tính chất demo. Trong môi trường production, hãy xác định các giá trị phù hợp dựa trên phân tích khối lượng công việc của bạn.
{{% /notice %}}

```bash
cat << EOF > ui-scaling-policy.json
{
    "TargetValue": 1500,
    "PredefinedMetricSpecification": {
        "PredefinedMetricType": "ALBRequestCountPerTarget",
        "ResourceLabel": "$UI_ALB_PREFIX/$UI_TG_PREFIX"
    }
}
EOF
```

2. Áp dụng chính sách scaling:

```bash
aws application-autoscaling put-scaling-policy \
    --service-namespace ecs \
    --scalable-dimension ecs:service:DesiredCount \
    --resource-id service/retail-store-ecs-cluster/ui \
    --policy-name ui-scaling-policy --policy-type TargetTrackingScaling \
    --target-tracking-scaling-policy-configuration file://ui-scaling-policy.json
```

#### Cấu hình CloudWatch Alarm

Chính sách scaling tự động tạo hai CloudWatch alarm. Xem các alarm này trong [CloudWatch Alarms console](https://console.aws.amazon.com/cloudwatch/home#alarmsV2:).

{{% notice note %}}
Trạng thái alarm phản ánh khối lượng request hiện tại. Alarm UI-AlarmLow kích hoạt khi số request giảm xuống dưới 1350.
{{% /notice %}}

![alt text](/images/3-target-tracking-scaling/image-3.png)
*Hình 2. CloudWatch Alarms Dashboard*

**Cấu hình Scale-out Alarm:**
- Ngưỡng metric: ALBRequestCountPerTarget vượt quá 1500
- Thời gian đánh giá: 3 điểm dữ liệu liên tiếp trong 3 phút
- Hành động: Tăng số lượng task khi ở trạng thái ALARM
- Hành vi scaling: Thêm task dần dần cho đến khi metric bình thường hoặc đạt công suất tối đa

![alt text](/images/3-target-tracking-scaling/image.png)
*Hình 3. Service scaling out dựa trên metric ALBRequestCountPerTarget*

**Cấu hình Scale-in Alarm:**
- Ngưỡng metric: ALBRequestCountPerTarget dưới 1350
- Thời gian đánh giá: 15 điểm dữ liệu liên tiếp trong 15 phút
- Hành động: Giảm số lượng task khi ở trạng thái ALARM
- Hành vi scaling: Scale-in từ từ để đảm bảo tính ổn định của service

![alt text](/images/3-target-tracking-scaling/image-1.png)
*Hình 4. Service scaling in dựa trên metric ALBRequestCountPerTarget*