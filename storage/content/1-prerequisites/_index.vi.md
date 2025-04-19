---
title: "Điều kiện tiên quyết"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: "<b> 1. </b>"
---

Trước khi bắt đầu các bài thực hành phần Storage, bạn cần hoàn thành một số nhiệm vụ tiên quyết để đảm bảo chức năng hoạt động đúng:

1. Tạo security group cho Amazon EFS để cho phép truy cập an toàn vào file system
2. Cấu hình ECS IAM Role với các quyền thích hợp để quản lý EFS
3. Thiết lập chuyển file từ môi trường local của bạn đến EC2 instance có gắn EFS storage

Những điều kiện tiên quyết này là cần thiết để thiết lập kết nối an toàn giữa các service và cho phép truy cập file system đúng cách.

#### Các bước tiên quyết

Hoàn thành các nhiệm vụ theo thứ tự:

1. [Tạo Security Group](1.1-create-security-group/)
2. [Tạo EFS File System](1.2-create-efs/)
3. [Upload Images lên Public EC2 Instance](1.3-uploading-images-to-ec2/)

Mỗi bước cung cấp hướng dẫn chi tiết để cấu hình các thành phần cần thiết cho môi trường workshop. Hãy đảm bảo hoàn thành tất cả các điều kiện tiên quyết trước khi tiếp tục với các bài thực hành chính.