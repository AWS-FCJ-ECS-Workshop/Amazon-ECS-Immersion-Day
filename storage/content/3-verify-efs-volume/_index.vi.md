---
title: "Xác minh Amazon EFS Volume"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: "<b> 3. </b>"
---

Sau khi tích hợp Amazon EFS với Amazon ECS, hãy xác minh rằng EFS volume được gắn kết và hoạt động đúng cách. Ban đầu, ứng dụng hiển thị các biểu tượng hình ảnh bị hỏng vì EFS volume đang trống. Để truy cập ứng dụng, lấy và sử dụng URL của Application Load Balancer:

```bash
export RETAIL_ALB=$(aws elbv2 describe-load-balancers --name retail-store-ecs-ui \
 --query 'LoadBalancers[0].DNSName' --output text)

echo http://${RETAIL_ALB} ; echo
```

{{% notice note %}}
Xóa cache trình duyệt để đảm bảo bạn thấy được những thay đổi mới nhất.
{{% /notice %}}

![web browser with broken image](https://static.us-east-1.prod.workshops.aws/public/fe1738fc-3d5c-4d22-bac3-0be10a3ad36f/static/images/90-storage/ui-service-webapp-broken-image.png)

Để kiểm tra nội dung thư mục container, thiết lập một phiên tương tác:

```bash
ECS_EXEC_TASK_ARN=$(aws ecs list-tasks --cluster retail-store-ecs-cluster \
    --service-name assets --query 'taskArns[]' --output text | \
    xargs -n1 aws ecs describe-tasks --cluster retail-store-ecs-cluster --tasks | \
    jq -r '.tasks[] | select(.enableExecuteCommand == true) | .taskArn' | \
    head -n 1)

if [ -z ${ECS_EXEC_TASK_ARN} ]; then echo "ECS_EXEC_TASK_ARN is not correctly configured!"; else
aws ecs execute-command --cluster retail-store-ecs-cluster \
    --task $ECS_EXEC_TASK_ARN \
    --container application \
    --interactive \
    --command "/bin/bash"
fi
```

Kiểm tra nội dung thư mục:

```bash
ls -al /usr/share/nginx/html/assets
```

Kết quả xác nhận thư mục trống:

```bash
bash-5.2# ls -al /usr/share/nginx/html/assets
total 8
drwxr-xr-x 2 root  root  6144 Oct 22 05:18 .
drwxr-xr-x 1 nginx nginx 4096 Jan  9  2024 ..
```

Thoát phiên tương tác:

```bash
exit
```

#### Nhập hình ảnh mới vào EFS Volume

Sử dụng EFS volume được gắn kết trong [môi trường EC2](1-prerequisites/1.3-uploading/images-to-ec2), nhập trực tiếp hình ảnh. Đầu tiên, xác minh thông tin gắn kết EFS:

```bash
df -h
```

![alt text](/images/3-verify-efs-volume/image-1.png)
*Hình 1. Kết quả điểm gắn kết EFS*

Chuyển hình ảnh vào điểm gắn kết EFS:

```bash
sudo cp images/* efs-mount-point/
```

![alt text](/images/3-verify-efs-volume/image.png)
*Hình 2. Hình ảnh trong `efs-mount-point/`*

#### Xác minh hình ảnh đã cập nhật

Làm mới trình duyệt để xem các hình ảnh sản phẩm mới được thêm vào.

{{% notice tip %}}
Nếu hình ảnh không xuất hiện ngay lập tức, hãy xóa cache trình duyệt.
{{% /notice %}}

![web site with updated images](https://static.us-east-1.prod.workshops.aws/public/fe1738fc-3d5c-4d22-bac3-0be10a3ad36f/static/images/90-storage/ui-service-webapp-updatedasset.png)

Xác minh việc gắn kết EFS volume trong container bằng cách bắt đầu một phiên tương tác khác:

```bash
ECS_EXEC_TASK_ARN=$(aws ecs list-tasks --cluster retail-store-ecs-cluster \
    --service-name assets --query 'taskArns[]' --output text | \
    xargs -n1 aws ecs describe-tasks --cluster retail-store-ecs-cluster --tasks | \
    jq -r '.tasks[] | select(.enableExecuteCommand == true) | .taskArn' | \
    head -n 1)

if [ -z ${ECS_EXEC_TASK_ARN} ]; then echo "ECS_EXEC_TASK_ARN is not correctly configured!"; else
aws ecs execute-command --cluster retail-store-ecs-cluster \
    --task $ECS_EXEC_TASK_ARN \
    --container application \
    --interactive \
    --command "/bin/bash"
fi
```

Kiểm tra nội dung EFS volume đã gắn kết:

```bash
ls -al /usr/share/nginx/html/assets
```

Kết quả bây giờ sẽ hiển thị các hình ảnh mới được sao chép:

```
bash-5.2# ls -al /usr/share/nginx/html/assets
total 312
drwxr-xr-x 2 root  root   6144 Oct 22 06:04 .
drwxr-xr-x 1 nginx nginx  4096 Jan  9  2024 ..
-rw-r--r-- 1 root  root  43231 Oct 22 06:03 chrono_classic.jpg
-rw-r--r-- 1 root  root  71709 Oct 22 06:03 gentleman.jpg
-rw-r--r-- 1 root  root  53214 Oct 22 06:03 pocket_watch.jpg
-rw-r--r-- 1 root  root  27844 Oct 22 06:03 smart_1.jpg
-rw-r--r-- 1 root  root  45312 Oct 22 06:03 smart_2.jpg
-rw-r--r-- 1 root  root  37255 Oct 22 06:03 smart_3.jpg
-rw-r--r-- 1 root  root  20280 Oct 22 06:03 wood_watch.jpg
```

Thoát phiên tương tác:

```bash
exit
```