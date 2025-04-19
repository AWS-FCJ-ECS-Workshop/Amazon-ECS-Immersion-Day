---
title: "Tải ảnh lên Public EC2"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: "<b> 3. </b>"
---

1. Kết nối tới Public EC2 Instance
- Nếu bạn chưa tạo EC2 instance, vui lòng làm theo hướng dẫn tại [Thiết lập môi trường lab](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/fundamentals/2-prerequisites/6-set-up-lab-environment/)

Điều hướng đến EC2 Dashboard > Chọn Public EC2 instance của bạn > Nhấp Connect
![alt text](/images/1-prerequisites/1.3-uploading-images-to-ec2/image.png)
*Hình 1. EC2 Dashboard*

Sao chép lệnh SSH mẫu và sửa đổi đường dẫn SSH key để kết nối tới EC2 instance của bạn
![alt text](/images/1-prerequisites/1.3-uploading-images-to-ec2/image-1.png)
*Hình 2. Chi tiết kết nối SSH EC2*

Xác nhận kết nối SSH thành công
![alt text](/images/1-prerequisites/1.3-uploading-images-to-ec2/image-2.png)
*Hình 3. Kết nối SSH thành công tới EC2*

2. Gắn EFS Volume vào EC2 Instance
    a. Tạo và cấu hình điểm gắn kết
    
    ```bash
    mkdir efs-mount-point/    # Tạo thư mục cho EFS mount
    sudo yum install -y amazon-efs-utils
    sudo mount -t efs fs-078993d4880a9e44d efs-mount-point/    # Gắn EFS volume
    ```
    
    ![alt text](/images/1-prerequisites/1.3-uploading-images-to-ec2/image-3.png)
    *Hình 4. Tạo thư mục gắn kết*
    
    b. Chuyển ảnh từ máy local lên `efs-mount-point` của EC2
    
    Đảm bảo bạn có thư mục `images` chứa các ảnh cần thiết. Bạn có thể sử dụng ảnh của riêng mình hoặc [tải xuống các ảnh mẫu](https://drive.google.com/drive/folders/1Zv327_rLX_Skp_NWZ0FqkA886-WEmdrn?usp=drive_link). Tên ảnh phải khớp chính xác như bên dưới để tương thích với ứng dụng Assets.

    ![alt text](/images/1-prerequisites/1.3-uploading-images-to-ec2/image-4.png)
    *Hình 5. Nội dung thư mục images local*
    

    Sử dụng SCP (Secure Copy Protocol) để chuyển file từ máy local lên EC2 instance:

    ```bash
    # Định dạng: scp -i /path/to/ssh-key -r /path/to/local/directory username@remote-host:/destination/path
    sudo scp -i ~/.ssh/ecs-workshop-key-pairs.pem -r ~/Desktop/images/ ec2-user@ec2-54-79-164-169.ap-southeast-2.compute.amazonaws.com:.
    ```
    
    ![alt text](/images/1-prerequisites/1.3-uploading-images-to-ec2/image-5.png)
    *Hình 6. Chuyển file lên EC2 thành công*

    Chúng ta đã tải lên thành công các hình ảnh vào EC2 instance. Trong các bước tiếp theo, chúng ta sẽ sao chép các hình ảnh này vào thư mục đã được mount.