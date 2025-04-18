---
title: "Xác minh Amazon EFS Volume"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: "<b> 3. </b>"
---

<!-- TODO: Verify section này -->

Sau khi tích hợp Amazon EFS với Amazon ECS, hãy xác minh chức năng của EFS volume đã được mount. Ban đầu, ứng dụng hiển thị các biểu tượng hình ảnh bị hỏng vì EFS volume đang trống. Truy cập URL ứng dụng bằng cách:

```bash
export RETAIL_ALB=$(aws elbv2 describe-load-balancers --name retail-store-ecs-ui \
 --query 'LoadBalancers[0].DNSName' --output text)

echo http://${RETAIL_ALB} ; echo
```

{{% notice note %}}
Xóa cache trình duyệt để xem các thay đổi mới nhất.
{{% /notice %}}

![web browser with broken image](https://static.us-east-1.prod.workshops.aws/public/fe1738fc-3d5c-4d22-bac3-0be10a3ad36f/static/images/90-storage/ui-service-webapp-broken-image.png)

Kiểm tra nội dung thư mục container bằng cách bắt đầu phiên tương tác:

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

Xem nội dung thư mục:

```bash
ls -al /usr/share/nginx/html/assets
```

Kết quả hiển thị một thư mục trống:

```bash
bash-5.2# ls -al /usr/share/nginx/html/assets
total 8
drwxr-xr-x 2 root  root  6144 Oct 22 05:18 .
drwxr-xr-x 1 nginx nginx 4096 Jan  9  2024 ..
```

Thoát phiên:

```bash
exit
```

#### Import hình ảnh mới vào EFS Volume

EFS volume được mount trong môi trường IDE cho phép import hình ảnh trực tiếp. Xác minh thông tin mount EFS:

```bash
df -h
```

Tìm điểm mount EFS trong kết quả:

```
Filesystem        Size  Used Avail Use% Mounted on
devtmpfs          4.0M     0  4.0M   0% /dev
tmpfs             1.9G     0  1.9G   0% /dev/shm
tmpfs             766M  516K  766M   1% /run
/dev/nvme0n1p1     32G  3.3G   29G  11% /
tmpfs             1.9G   77M  1.8G   4% /tmp
/dev/nvme0n1p128   10M  1.3M  8.7M  13% /boot/efi
tmpfs             383M     0  383M   0% /run/user/0
127.0.0.1:/       8.0E     0  8.0E   0% /home/ec2-user/environment/labs/efs/mount-point
```

Sao chép hình ảnh mới vào điểm mount EFS:

```bash
sudo cp ~/environment/labs/efs/images/* ~/environment/labs/efs/mount-point/
```

#### Xác minh hình ảnh đã cập nhật

Làm mới trình duyệt để xem hình ảnh sản phẩm đã cập nhật.

{{% notice tip %}}
Xóa cache trình duyệt để đảm bảo bạn thấy các thay đổi mới nhất.
{{% /notice %}}

![web site with updated images](https://static.us-east-1.prod.workshops.aws/public/fe1738fc-3d5c-4d22-bac3-0be10a3ad36f/static/images/90-storage/ui-service-webapp-updatedasset.png)

Xác nhận việc mount EFS volume trong container bằng cách bắt đầu một phiên tương tác khác:

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

Kiểm tra nội dung EFS volume đã mount:

```bash
ls -al /usr/share/nginx/html/assets
```

Kết quả sẽ hiển thị các hình ảnh mới được sao chép:

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

Thoát phiên:

```bash
exit
```