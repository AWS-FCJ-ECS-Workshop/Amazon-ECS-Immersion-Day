---
title: "Verify Amazon EFS Volume"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: "<b> 3. </b>"
---

Now that we have integrated Amazon EFS with Amazon ECS, let's verify how we can utilize the mounted EFS volume. Since our application doesn't yet have the "add/delete product" feature, we'll import the updated images directly through our IDE.

When you first access the application in a web browser, you'll notice broken image icons. This occurs because the EFS volume doesn't yet contain any images. You can verify this by accessing the application's URL:

```bash
    export RETAIL_ALB=$(aws elbv2 describe-load-balancers --name retail-store-ecs-ui \
     --query 'LoadBalancers[0].DNSName' --output text)
    
    echo http://${RETAIL_ALB} ; echo
```

Tip

You may need to clear your browser's cache to see the changes.

![web browser with broken image](https://static.us-east-1.prod.workshops.aws/public/fe1738fc-3d5c-4d22-bac3-0be10a3ad36f/static/images/90-storage/ui-service-webapp-broken-image.png)

Let's verify the directory contents within the container. First, start a `/bin/bash` interactive session in the running task:

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

Then check the directory contents:

```bash
    ls -al /usr/share/nginx/html/assets
```

You'll see that the directory is empty:

```bash
    bash-5.2# ls -al /usr/share/nginx/html/assets
    total 8
    drwxr-xr-x 2 root  root  6144 Oct 22 05:18 .
    drwxr-xr-x 1 nginx nginx 4096 Jan  9  2024 ..
```

Exit the session:

```bash
    exit
```

#### Import New Images to the EFS Volume

Since the EFS volume is mounted in our IDE environment, we can easily import the new images through the IDE.

First, let's check the current EFS mount information in the IDE:

```bash
    df -h
```

You'll see output similar to this:

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

Note that the EFS volume is mounted at **_/home/ec2-user/environment/labs/efs/mount-point_**. Let's copy the new images from **_/home/ec2-user/environment/labs/efs/images_** to the EFS mount point:

```bash
    sudo cp ~/environment/labs/efs/images/* ~/environment/labs/efs/mount-point/
```

#### Verify the Updated Images

After copying the images, reload your browser to see the updated product images.

Tip

You may need to clear your browser's cache to see the changes.

![web site with updated images](https://static.us-east-1.prod.workshops.aws/public/fe1738fc-3d5c-4d22-bac3-0be10a3ad36f/static/images/90-storage/ui-service-webapp-updatedasset.png)

Let's verify that the EFS volume is properly mounted in the container. Start another `/bin/bash` interactive session in the running task:

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

Check the contents of the mounted EFS volume in the container:

```bash
    ls -al /usr/share/nginx/html/assets
```

You should see the following output:

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

Exit the session:

```bash
    exit
```