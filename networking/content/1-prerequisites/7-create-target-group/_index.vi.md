---
title: "Tạo UI Target Group"
date: "`r Sys.Date()`"
weight: 7
chapter: false
pre: "<b> 7. </b>"
---

Trong phần này, chúng ta sẽ tạo một Target Group cho `ui-application-tls` để hỗ trợ giao thức HTTPS.

#### Tạo Target Group
1. Điều hướng đến [EC2 Target Groups](https://console.aws.amazon.com/ec2/home#TargetGroups:)
2. Nhấp vào **Create Target Group**

Cấu hình các thiết lập sau trong Specify group details:
- Chọn `IP Addresses` làm target type

![alt text](/images/1-prerequisites/7-create-target-group/image.png)
*Hình 1. Specify Group Details*

Cấu hình cơ bản:
- Target group name: `ui-application-tls`
- Protocol: `HTTPS 8080`
- VPC: `ecs-lab-vpc`

![alt text](/images/1-prerequisites/7-create-target-group/image-1.png)
*Hình 2. Target Group Configuration*

Cấu hình kiểm tra sức khỏe:
- Protocol: HTTP
- Health check path: `actuator/health`
- Health check port: 8080 (chọn từ dropdown)

![alt text](/images/1-prerequisites/7-create-target-group/image-2.png)
*Hình 3. Health Check Configuration*

Đăng ký targets:
- VPC: `ecs-lab-vpc`
- Port: 8080

![alt text](/images/1-prerequisites/7-create-target-group/image-3.png)
*Hình 4. Register Target*

Xem lại và tạo target group

![alt text](/images/1-prerequisites/7-create-target-group/image-4.png)
*Hình 5. Target Group Creation Complete*

Xuất ARN của Target Group dưới dạng biến môi trường:

```bash
export UI_TARGET_GROUP_TLS_ARN=arn:aws:elasticloadbalancing:ap-southeast-2:XXXXXXXXXXX:targetgroup/ui-application-tls/c25180349d64205c
```

✅ Bạn đã tạo thành công một Target Group cho UI TLS