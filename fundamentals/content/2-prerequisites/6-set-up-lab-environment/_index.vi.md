---
title: "Thiết lập môi trường Lab"
date: "`r Sys.Date()`"
weight: 6
chapter: false
pre: "<b> 2.6. </b>"
---

Trong phần này, bạn sẽ thiết lập EC2 cùng với các tài nguyên mạng cần thiết cho bài thực hành. Việc thiết lập này là cần thiết vì trong phần [Storage](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/storage), chúng ta sẽ có các dịch vụ tài nguyên (asset services) và cần phải Mount File System vào EC2 để cập nhật tài nguyên.

![Sơ đồ môi trường mạng ECS-Lab](/images/2-prerequisites/6-set-up-lab-environment/ECS-Lab-Networking-EC2-environment.png)

### Khởi tạo EC2 Instance

1. Truy cập EC2 Dashboard và chọn **Launch Instance**

2. Trong giao diện Launch Instance:
   - Chọn Amazon Linux 2023 AMI
   - Loại instance: t2.micro 
   - Key pairs: Có thể tạo mới hoặc sử dụng key có sẵn

![Giao diện khởi tạo EC2](/images/2-prerequisites/6-set-up-lab-environment/image.png)

3. Trong phần Network Settings:
   - Chọn **Edit**
   - VPC: Chọn `ecs-lab-vpc`
   - Subnet: Chọn `ecs-lab-subnet-public1` 
   - Bật tự động gán địa chỉ IP
   - Security group: Chọn security group có sẵn `ecs-lab-public-sg` (cho phép HTTPS, HTTP và SSH)

![Cấu hình mạng EC2](/images/2-prerequisites/6-set-up-lab-environment/image-1.png)

4. Chọn **Launch instance**

### Cấp quyền cho EC2 Instance

Để cho phép EC2 instance thực thi các lệnh AWS CLI thay bạn, chúng ta cần phải gán một IAM Role cho EC2 Service. Trong bài lab này, chúng ta sẽ tạo một IAM Role (được EC2 assume) và gán Policy `AdministratorAccess` cho role này.

{{% notice warning %}}
Việc sử dụng `AdministratorAccess` Policy chỉ phù hợp cho môi trường phát triển (development). Khi triển khai hệ thống trong môi trường sản phẩm (Production), bạn nên tuân thủ nguyên tắc least privilege - chỉ cấp những quyền tối thiểu cần thiết cho Users và Services.
{{% /notice %}}

#### Các bước thực hiện:

1. **Tạo IAM Role mới**
   - Truy cập [IAM Roles Dashboard](https://console.aws.amazon.com/iam/home#/roles)
   - Chọn **Create Role**

![alt text](/images/2-prerequisites/6-set-up-lab-environment/image-5.png)

2. **Thiết lập Trust Policy**
   - Trong phần **Select trusted entity**:
     - Chọn **AWS Services**
     - Use case: **EC2**
   - Chọn **Next**

![alt text](/images/2-prerequisites/6-set-up-lab-environment/image-6.png)

3. **Thêm Permissions**
   - Tìm và chọn policy **AdministratorAccess**
   - Chọn **Next**

![alt text](/images/2-prerequisites/6-set-up-lab-environment/image-7.png)

4. **Đặt tên và xem lại cấu hình**
   - Nhập tên Role
   - Kiểm tra lại các cấu hình
   - Chọn **Create Role**

![alt text](/images/2-prerequisites/6-set-up-lab-environment/image-8.png)
![alt text](/images/2-prerequisites/6-set-up-lab-environment/image-9.png)

5. **Gán Role cho EC2 Instance**
   - Truy cập [EC2 Instances Dashboard](https://console.aws.amazon.com/ec2#Instances)
   - Chọn EC2 instance cần gán role
   - Từ menu **Actions**, chọn **Security** > **Modify IAM Role**

![alt text](/images/2-prerequisites/6-set-up-lab-environment/image-10.png)

   - Trong giao diện **Modify IAM Role**:
     - Chọn IAM Role `ecs-lab-admin-ec2`
     - Chọn **Update IAM Role**

![alt text](/images/2-prerequisites/6-set-up-lab-environment/image-11.png)

Sau khi hoàn tất các bước trên, EC2 instance của bạn đã được cấp quyền và có thể thực thi các lệnh AWS CLI để tương tác với các dịch vụ AWS khác.

### Kết nối SSH vào EC2

1. Sau khi tạo thành công, chọn EC2 instance và nhấn **Connect**

![Kết nối EC2](/images/2-prerequisites/6-set-up-lab-environment/image-2.png)

2. Chuyển sang tab SSH Client và sao chép lệnh SSH:
```bash
ssh -i "ecs-workshop-key-pairs.pem" ec2-user@ec2-54-253-11-210.ap-southeast-2.compute.amazonaws.com
```

![Lệnh SSH](/images/2-prerequisites/6-set-up-lab-environment/image-3.png)

3. Mở terminal, dán lệnh SSH và thực thi

{{% notice info %}}
⚠️ **Lưu ý**: Hãy điều chỉnh đường dẫn private key theo vị trí lưu trữ thực tế của bạn!
{{% /notice %}}

![Kết quả SSH](/images/2-prerequisites/6-set-up-lab-environment/image-4.png)

### Kết luận

Chúng ta đã tạo thành công một EC2 instance có khả năng gắn kết EFS để cập nhật hình ảnh sản phẩm trong các dịch vụ tài nguyên.