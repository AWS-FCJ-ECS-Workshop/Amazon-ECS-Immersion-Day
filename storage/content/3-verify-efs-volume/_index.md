---
title: "Verify Amazon EFS Volume"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: "<b> 3. </b>"
---

After integrating Amazon EFS with Amazon ECS, verify the mounted EFS volume functionality. Initially, the application displays broken image icons because the EFS volume is empty. Access the application URL using:

```bash
export RETAIL_ALB=$(aws elbv2 describe-load-balancers --name retail-store-ecs-ui \
 --query 'LoadBalancers[0].DNSName' --output text)

echo http://${RETAIL_ALB} ; echo
```

{{% notice note %}}
Clear your browser cache to view the latest changes.
{{% /notice %}}

![web browser with broken image](https://static.us-east-1.prod.workshops.aws/public/fe1738fc-3d5c-4d22-bac3-0be10a3ad36f/static/images/90-storage/ui-service-webapp-broken-image.png)

Inspect the container directory contents by starting an interactive session:

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

View the directory contents:

```bash
ls -al /usr/share/nginx/html/assets
```

The output shows an empty directory:

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

The EFS volume mounted in the [EC2 environment](1-prerequisites/1.3-uploading/images-to-ec2) allows direct image import. Verify the EFS mount information:

```bash
df -h
```

![alt text](image-1.png)
*Figure 1. EFS mount point output*

Copy the new images to the EFS mount point:

```bash
sudo cp images/* efs-mount-point/
```

![alt text](/images/3-verify-efs-volume/image.png)
*Figure 2. Images inside `efs-mount-point/`*

#### Verify the Updated Images

Refresh your browser to view the updated product images.

{{% notice tip %}}
Clear your browser cache to ensure you see the latest changes.
{{% /notice %}}

![web site with updated images](https://static.us-east-1.prod.workshops.aws/public/fe1738fc-3d5c-4d22-bac3-0be10a3ad36f/static/images/90-storage/ui-service-webapp-updatedasset.png)

Confirm the EFS volume mounting in the container by starting another interactive session:

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

Check the mounted EFS volume contents:

```bash
ls -al /usr/share/nginx/html/assets
```

The output should display the newly copied images:

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