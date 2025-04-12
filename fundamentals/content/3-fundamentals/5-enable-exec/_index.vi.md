---
title: "Kích hoạt ECS Exec"
date: "`r Sys.Date()`"
weight: 5
chapter: false
pre: "<b> 3.5. </b>"
---

### Enable ECS Exec

Phần này sẽ hướng dẫn bạn kích hoạt tính năng ECS Exec để chạy lệnh hoặc truy cập shell vào container đang chạy trên EC2 instance hoặc Fargate. ECS Exec mang lại nhiều lợi ích trong quản lý vận hành và bảo mật. Tính năng này cho phép kiểm soát truy cập vào các container trong ECS tasks, hỗ trợ khắc phục sự cố một cách an toàn và có ghi nhật ký mà không cần SSH vào máy chủ.

Thông qua việc sử dụng chính sách và IAM Role, bạn có thể kiểm soát chặt chẽ những người được phép thực thi lệnh trong container, từ đó nâng cao tính bảo mật tổng thể. Ngoài ra, tất cả các lệnh được thực thi qua ECS Exec đều được ghi lại trong CloudWatch, tạo ra dấu vết kiểm toán phục vụ cho việc tuân thủ và giám sát. Thông tin chi tiết có thể được tìm thấy [tại đây](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-exec.html).

Thực hiện theo các bước sau để kích hoạt và sử dụng ECS Exec:

1. Thiết lập IAM Role cho người dùng
2. Thiết lập IAM Role cho ECS task
3. Thiết lập môi trường
4. Kích hoạt Amazon ECS Exec trên service
5. Kết nối tới ECS task

---
#### Thiết lập IAM Role cho người dùng

Vì bạn sẽ sử dụng ECS Exec từ IDE, hãy đảm bảo IAM Role gắn với IDE có các chính sách IAM cần thiết. Cập nhật IAM Role được liên kết với EC2 instance đang chạy IDE bằng cách thêm chính sách inline sau:

```bash
cat << EOF > ecs-exec-command-policy.json
{
    "Version": "2012-10-17", 
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ecs:ExecuteCommand",
                "ecs:DescribeTasks"
            ],
            "Resource": [
                "arn:aws:ecs:${AWS_REGION}:${ACCOUNT_ID}:task/retail-store-ecs-cluster/*",
                "arn:aws:ecs:${AWS_REGION}:${ACCOUNT_ID}:cluster/*"
            ]
        }
    ]
}
EOF

aws iam put-role-policy \
    --role-name $(aws sts get-caller-identity --query 'Arn' | cut -d'/' -f2) \
    --policy-name AmazonECSExecCommand \
    --policy-document file://ecs-exec-command-policy.json
```

<details>
<summary>Kiểm tra xem chính sách đã được cấu hình chính xác chưa</summary>
Trong bài thực hành này, bạn chỉ thêm chính sách IAM cơ bản nhất. Có thể cần thêm các chính sách IAM khác để sử dụng các tính năng ECS Exec như CloudWatch logs.
</details>

#### Thiết lập IAM Role cho ECS Task

ECS Exec cần một IAM Role task cho giao tiếp SSM. Chúng ta đã tạo sẵn IAM Role này cho UI Service task. Hãy kiểm tra quyền của nó:

```bash
aws iam get-role-policy \
    --role-name  retailStoreEcsTaskRole \
    --policy-name $(aws iam list-role-policies --role-name retailStoreEcsTaskRole --query 'PolicyNames[0]' --output text)
```

Kết quả sẽ như sau:

```json
{
    "RoleName": "retailStoreEcsTaskRole",
    "PolicyName": "RetailStoreEcsTaskRoleDefaultPolicy3AF87919",
    "PolicyDocument": {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Action": [
                    "ssmmessages:CreateControlChannel",
                    "ssmmessages:CreateDataChannel", 
                    "ssmmessages:OpenControlChannel",
                    "ssmmessages:OpenDataChannel"
                ],
                "Resource": "*",
                "Effect": "Allow"
            }
        ]
    }
}
```

#### Thiết lập môi trường

Bạn cần AWS CLI và Session Manager plugin cho AWS CLI. Cả hai tiện ích này đã được cài đặt sẵn trong IDE cho workshop này. Vui lòng xem tài liệu được liên kết để thiết lập trong môi trường của riêng bạn.

#### Kích hoạt Amazon ECS Exec trên task

Cập nhật UI Service để kích hoạt Amazon ECS Exec bằng cờ `--enable-execute-command`:

```bash
aws ecs update-service \
    --cluster retail-store-ecs-cluster \
    --service ui \
    --task-definition retail-store-ecs-ui \
    --enable-execute-command \
    --force-new-deployment
```

Đợi ECS triển khai các thay đổi lên service (khoảng 5 phút):

```bash
aws ecs wait services-stable --cluster retail-store-ecs-cluster --services ui
```

Chạy lệnh sau để chọn một trong các UI task đang chạy với `enableExecuteCommand` được kích hoạt:

```bash
ECS_EXEC_TASK_ARN=$(aws ecs list-tasks --cluster retail-store-ecs-cluster \
    --service-name ui --query 'taskArns[]' --output text | \
    xargs -n1 aws ecs describe-tasks --cluster retail-store-ecs-cluster --tasks | \
    jq -r '.tasks[] | select(.enableExecuteCommand == true) | .taskArn' | \
    head -n 1)
```

Kiểm tra kết quả bằng lệnh echo:

```bash
echo $ECS_EXEC_TASK_ARN
# arn:aws:ecs:ap-southeast-2:XXXXXXXXXXX:task/retail-store-ecs-cluster/0a2039141c054846b4dfc7983e49f64a
```

---
### Kết nối tới ECS Task

Bắt đầu phiên tương tác `/bin/bash` trong task đang chạy:

```bash
if [ -z ${ECS_EXEC_TASK_ARN} ]; then echo "ECS_EXEC_TASK_ARN is not correctly configured!"; else
aws ecs execute-command --cluster retail-store-ecs-cluster \
    --task $ECS_EXEC_TASK_ARN \
    --container application \
    --interactive \
    --command "/bin/bash"
fi
```

Bạn sẽ thấy kết quả như sau:

```
The Session Manager plugin was installed successfully. Use the AWS CLI to start a session.

Starting session with SessionId: ecs-execute-command-vvdysulqbcz2txr2d262sw2s64
bash-5.2#
```

Trong phiên mới, bạn có thể thực thi các lệnh sau:

```bash
cat /etc/os-release

df -h
```

![alt text](/images/3-fundamentals/5-enable-exec/image-1.png)

Để kết thúc phiên:

```bash
exit
```