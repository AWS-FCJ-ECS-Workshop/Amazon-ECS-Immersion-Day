---
title : "Scheduled Scaling"
date :  "`r Sys.Date()`" 
weight : 5
chapter : false
pre: "<b> 5. </b>"
---

{{% notice info %}}
Quan trọng
Bạn phải hoàn thành các chương sau như điều kiện tiên quyết cho bài lab này:
[Target Tracking Scaling](/3-target-tracking-scaling)
{{% /notice %}}

Phần này hướng dẫn cách cấu hình Amazon ECS Service Auto Scaling sử dụng các chính sách scaling theo lịch trình. Scheduled scaling cho phép bạn tự động điều chỉnh năng lực service dựa trên các mẫu tải có thể dự đoán được bằng các quy tắc dựa trên thời gian. Chúng ta sẽ cấu hình service UI để scale out và in sử dụng biểu thức cron.

![Scale out vs scale in](/images/5-scheduled-scaling/image-3.png)
*Hình 1. Scale out vs scale in*

#### Kiểm tra Scheduled Scaling

Để minh họa, chúng ta sẽ tạo các hành động scaling thực thi sau 2 phút khi chạy lệnh. Trong môi trường production, hãy lên lịch các hành động scaling theo các mẫu tải công việc nhất quán.

Lưu ý: Service UI đã được đăng ký trước đó như một scalable target với Application Auto Scaling trong phần Target Tracking.

Thực thi lệnh sau để tăng dung lượng tối thiểu từ 2 lên 5 task trong 2 phút:

```bash
aws application-autoscaling put-scheduled-action \
 --service-namespace ecs \
 --scalable-dimension ecs:service:DesiredCount \
 --resource-id service/retail-store-ecs-cluster/ui \
 --scheduled-action-name "test-scale-up" \
 --schedule "at($(date -u -d "+2 minutes" "+%Y-%m-%dT%H:%M:%S"))" \
 --scalable-target-action MinCapacity=5,MaxCapacity=10
```

Đặt bộ đếm thời gian để xác minh hành động đã lên lịch:

```bash
echo "Starting timer for 2 plus minutes..." && sleep 150 && echo "Time's up! and you can run the next command"
```

Sau khi bộ đếm thời gian hoàn tất, xác minh hành động scaling bằng cách kiểm tra các task ECS:

![Test scale out](/images/5-scheduled-scaling/image-1.png)
*Hình 2. Test scale out*

```bash
aws ecs describe-tasks \
 --cluster retail-store-ecs-cluster \
 --tasks $(aws ecs list-tasks --cluster retail-store-ecs-cluster --service-name ui --query 'taskArns[]' --output text) \
 --query "tasks[*].[group, launchType, lastStatus, healthStatus, taskArn]" --output table
```

Scale trở lại dung lượng ban đầu bằng cách thực thi:

```bash
aws application-autoscaling put-scheduled-action \
 --service-namespace ecs \
 --scalable-dimension ecs:service:DesiredCount \
 --resource-id service/retail-store-ecs-cluster/ui \
 --scheduled-action-name "test-scale-in" \
 --schedule "at($(date -u -d "+2 minutes" "+%Y-%m-%dT%H:%M:%S"))" \
 --scalable-target-action MinCapacity=2,MaxCapacity=10
```

![Test scale in](/images/5-scheduled-scaling/image-2.png)
*Hình 3. Test scale in*

#### Scheduled Scaling trong Production

Đối với khối lượng công việc production, cấu hình các hành động scaling dựa trên mẫu sử dụng của ứng dụng. Các ví dụ sau minh họa việc lên lịch cho một ứng dụng toàn cầu với thời điểm sử dụng cao nhất vào 9 giờ sáng UTC và 6 giờ chiều UTC hàng ngày.

![Real-world scheduled scaling](/images/5-scheduled-scaling/image.png)
*Hình 4. Real-world scheduled scaling*

Scale out lên 5 task vào 9:00 sáng UTC các ngày trong tuần:

```bash
aws application-autoscaling put-scheduled-action \
    --service-namespace ecs \
    --scalable-dimension ecs:service:DesiredCount \
    --resource-id service/retail-store-ecs-cluster/ui \
    --scheduled-action-name "week-day-scale-out" \
    --schedule "cron(0 9 ? * MON-FRI *)" \
    --scalable-target-action MinCapacity=5,MaxCapacity=10
```

Scale in xuống 2 task vào 6:00 chiều UTC các ngày trong tuần:

```bash
aws application-autoscaling put-scheduled-action \
    --service-namespace ecs \
    --scalable-dimension ecs:service:DesiredCount \
    --resource-id service/retail-store-ecs-cluster/ui \
    --scheduled-action-name "week-day-scale-in" \
    --schedule "cron(0 18 ? * MON-FRI *)" \
    --scalable-target-action MinCapacity=2,MaxCapacity=10
```

{{% notice info %}}
Lưu ý: Do chênh lệch múi giờ, các ví dụ production này có thể không thực thi trong workshop.
{{% /notice %}}