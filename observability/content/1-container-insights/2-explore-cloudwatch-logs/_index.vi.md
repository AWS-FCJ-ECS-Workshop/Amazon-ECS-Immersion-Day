+++
title = "Khám phá CloudWatch Logs"
date = 2020-05-14T00:38:32+07:00
weight = 1
chapter = false
pre = "<b>1.2 </b>"
+++

Hãy xem xét các nhật ký từ các ứng dụng của cluster cửa hàng bán lẻ và kiểm tra các nhật ký liên quan đến hiệu suất của cluster này.

### Khám phá Application Log

Chúng tôi đã cấu hình các cài đặt nhật ký cho dịch vụ container UI trong [ECS task definition section](https://catalog.workshops.aws/ecs-immersion-day/en-US/30-basic/200-task-definition/) như sau. Chúng tôi đã sử dụng trình điều khiển awslogs mặc định để thu thập nhật ký từ container và một **log group**(nhóm nhật ký), đại diện cho một nhóm các log stream (luồng nhật ký) có chung cài đặt về thời gian lưu giữ, giám sát và kiểm soát truy cập.

```
"logConfiguration": {
    "logDriver": "awslogs",
    "options": {
        "awslogs-group": "retail-store-ecs-tasks",
        "awslogs-region": "$AWS_REGION",
        "awslogs-stream-prefix": "ui-service"
    }
}
```

Điều hướng đến menu **Log groups** (Nhóm nhật ký) trong **CloudWatch**. Tìm kiếm log group **retail-store-ecs-tasks** và nhấp vào nó để xem các log stream liên quan đến UI và catalog như hình bên dưới.

![image-1](/images/2/image-1.png?width=90pc)
![image-2](/images/2/image-2.png?width=90pc)

{{% notice note %}}
Một **log stream** (luồng nhật ký) bao gồm một chuỗi các sự kiện nhật ký bắt nguồn từ cùng một nguồn. Mỗi nguồn nhật ký riêng biệt trong CloudWatch Logs tạo thành một log stream riêng của nó.
{{% /notice %}}

Chuyển đến **Logs Insights** để phân tích dữ liệu nhật ký ứng dụng bằng CloudWatch Logs Insights. Chọn **retail-store-ecs-tasks** cho log group và chạy truy vấn sau. Kết quả là, bạn có thể truy xuất các nhật ký liên quan đến dịch vụ UI. Kết quả sẽ được hiển thị dưới dạng biểu đồ cột các sự kiện nhật ký trong log group này theo thời gian.

```
fields @timestamp, @logStream, @message
| filter @logStream like /catalog-service/
| sort @timestamp desc
| limit 10
```

![image-3](/images/2/image-3.png?width=90pc)
![image-4](/images/2/image-4.png?width=90pc)
