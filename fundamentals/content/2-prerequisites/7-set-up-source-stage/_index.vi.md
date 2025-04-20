---
title: "Thiết lập giai đoạn lưu trữ mã nguồn"
date: "`r Sys.Date()`"
weight: 7
chapter: false
pre: "<b> 2.7. </b>"
---

Phần này hướng dẫn chuẩn bị repository mã nguồn, build Docker image và đẩy lên Amazon Elastic Container Registry (ECR) để hỗ trợ các tác vụ [automation](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/automation/vi/).

#### Kết nối tới EC2 Instance

1. Chọn EC2 instance đã tạo và click **Connect**

![EC2 Connect](/images/2-prerequisites/6-set-up-lab-environment/image-2.png)
*Hình 1: EC2 Dashboard*

2. Từ tab SSH Client, copy chuỗi kết nối SSH:
```bash
ssh -i "ecs-workshop-key-pairs.pem" ec2-user@ec2-54-253-11-210.ap-southeast-2.compute.amazonaws.com
```

![SSH Connection Details](/images/2-prerequisites/6-set-up-lab-environment/image-3.png)
*Hình 2: Chi tiết kết nối SSH*

3. Kết nối qua SSH sử dụng terminal

{{% notice info %}}
Đảm bảo cung cấp đúng đường dẫn tới file private key (.pem) trên máy tính của bạn.
{{% /notice %}}

![SSH Connection Success](/images/2-prerequisites/6-set-up-lab-environment/image-4.png)
*Hình 3: Kết nối SSH thành công*

#### Cài đặt Docker trên EC2

1. Cài đặt Docker:
```bash
sudo yum install -y docker
```

2. Khởi động service Docker:
```bash
sudo service docker start
```

3. Thêm user EC2 vào nhóm docker:
```bash
sudo usermod -a -G docker ec2-user
```

4. Đăng xuất và kết nối lại để áp dụng quyền mới:
```bash
exit
```

5. Kiểm tra cài đặt Docker:
```bash
docker ps
```

Kết quả mong đợi:
```
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS 
```

![Docker Installation Verification](/images/2-prerequisites/7-set-up-source-stage/image.png)
*Hình 4: Xác nhận cài đặt Docker*

#### Fork Repository

{{% notice note %}}
Mục đích: Tạo bản sao độc lập của mã nguồn trong repository cá nhân để thực hiện thay đổi mà không ảnh hưởng đến nguồn gốc.
{{% /notice %}}

1. Truy cập https://github.com/aws-containers/retail-store-sample-app/fork
2. Click "Create fork" để tạo bản sao cá nhân

![Fork Repository](/images/2-prerequisites/7-set-up-source-stage/image-9.png)
*Hình 5: Giao diện Fork Repository*

#### Thiết lập Git và Clone Repository

1. Cài đặt Git:
```bash
sudo yum install git -y
```

2. Clone và checkout phiên bản cụ thể:
```bash
git clone https://github.com/AWS-FCJ-ECS-Workshop/retail-store-sample-app.git
git checkout tags/0.8.5
```

![Clone Repository Success](/images/2-prerequisites/7-set-up-source-stage/image-1.png)
*Hình 6: Clone Repository thành công*

#### Build Docker Image cho UI Service

Di chuyển vào thư mục UI service và build image:
```bash
cd ui
docker build -t aws-container/retail-store-sample-ui:0.8.5 .
```

![UI Service Build Success](/images/2-prerequisites/7-set-up-source-stage/image-2.png)
*Hình 7: Build UI Service thành công*

Kiểm tra image:
```bash
docker run --rm -p 8080:8080 aws-container/retail-store-sample-ui:0.8.5
```

{{% notice info %}}
Cấu hình inbound rules cho port 8080 trong security group `ecs-lab-public-sg` để cho phép truy cập internet.
{{% /notice %}}

![UI Application](/images/2-prerequisites/7-set-up-source-stage/image-7.png)

#### Đẩy lên Amazon ECR

1. Thiết lập biến môi trường:
```bash
export AWS_REGION=ap-southeast-2
export AWS_ACCOUNT_ID=11111111111  # Thay bằng ID tài khoản của bạn
```

2. Tạo ECR repository:
```bash
aws ecr create-repository \
    --repository-name aws-containers/retail-store-sample-ui \
    --region $AWS_REGION
```

![ECR Repository Creation](/images/2-prerequisites/7-set-up-source-stage/image-3.png)
*Hình 8: Giao diện ECR*

3. Xác thực với ECR:
```bash
aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com
```

4. Tag và push image:
```bash
docker tag aws-container/retail-store-sample-ui:0.8.5 $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/aws-containers/retail-store-sample-ui:0.8.5
docker push $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/aws-containers/retail-store-sample-ui:0.8.5
```

![Image Push Success](/images/2-prerequisites/7-set-up-source-stage/image-5.png)
*Hình 9: Giao diện CLI cho việc push image lên ECR*

![ECR Console View](/images/2-prerequisites/7-set-up-source-stage/image-6.png)
*Hình 10: Giao diện Console ECR*

✔ Trong phần này, chúng ta đã fork repository thành công và build và push image lên ECR repository cá nhân để chuẩn bị cho chương [automation](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/automation/vi/) trong series này.