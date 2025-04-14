---
title: "Target Tracking Scaling"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: "<b> 3. </b>"
---

### Target Tracking Scaling

![alt text](/images/3-target-tracking-scaling/image-2.png)

*Hình 1. Service scaling dựa trên metric `ALBRequestCountPerTarget`*

Phần này hướng dẫn bạn cấu hình ECS Service Auto Scaling sử dụng Target Tracking Scaling. Bạn sẽ học cách thiết lập application autoscaling và triển khai các chính sách scaling phù hợp cho các service của bạn.

Đầu tiên, đăng ký UI service như một scalable target với Application Auto Scaling. Thực thi lệnh sau để thiết lập phạm vi scaling từ 2 đến 10 task:

```bash
aws application-autoscaling register-scalable-target \
    --service-namespace ecs \
    --scalable-dimension ecs:service:DesiredCount \
    --resource-id service/retail-store-ecs-cluster/ui \
    --min-capacity 2 \
    --max-capacity 10
```

Tạo chính sách scaling cho target bằng cách làm theo các bước sau:

1. Tạo file cấu hình JSON định nghĩa chính sách scaling. Cấu hình này sử dụng metric type ALB request count per target, nhắm đến 1.500 request cho mỗi ECS task.

{{% notice note %}}
Các giá trị chính sách scaling được hiển thị ở đây chỉ mang tính chất minh họa. Hãy phân tích mô hình tải của bạn để xác định các metric và ngưỡng scaling phù hợp cho môi trường của bạn.
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

2. Áp dụng chính sách scaling bằng lệnh này:

```bash
aws application-autoscaling put-scaling-policy \
    --service-namespace ecs \
    --scalable-dimension ecs:service:DesiredCount \
    --resource-id service/retail-store-ecs-cluster/ui \
    --policy-name ui-scaling-policy --policy-type TargetTrackingScaling \
    --target-tracking-scaling-policy-configuration file://ui-scaling-policy.json
```

### Cấu hình CloudWatch Alarm

Chính sách scaling tự động tạo hai CloudWatch alarm. Truy cập chúng trong [CloudWatch Alarms console](https://console.aws.amazon.com/cloudwatch/home#alarmsV2:).

{{% notice note %}}
Trạng thái alarm phụ thuộc vào lượng request hiện tại. Ví dụ, **UI-AlarmLow** kích hoạt khi số request giảm xuống dưới **1350**.
{{% /notice %}}

![alt text](/images/3-target-tracking-scaling/image-1.png)
*Hình 2. Bảng điều khiển CloudWatch Alarms*

**Cấu hình Scale-out (High) Alarm:**
- Ngưỡng metric: ALBRequestCountPerTarget > 1500
- Chu kỳ đánh giá: 3 điểm dữ liệu trong 3 phút
- Hành động: Thêm task khi trạng thái ALARM được kích hoạt
- Hành vi scaling: Tăng dần số task cho đến khi metric ổn định hoặc đạt công suất tối đa

**Cấu hình Scale-in (Low) Alarm:**
- Ngưỡng metric: ALBRequestCountPerTarget < 1350
- Chu kỳ đánh giá: 15 điểm dữ liệu trong 15 phút
- Hành động: Xóa task khi trạng thái ALARM được kích hoạt
- Hành vi scaling: Scale-in thận trọng để duy trì tính khả dụng của service