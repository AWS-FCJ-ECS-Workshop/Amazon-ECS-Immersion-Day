---
title: "Tạo DynamoDB"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: "<b> 2.2. </b>"
---

Trong phần này, chúng ta sẽ tạo một bảng DynamoDB để lưu trữ thông tin chi tiết giỏ hàng.

{{% notice info %}}
Sử dụng chính xác tên bảng và chỉ mục được chỉ định dưới đây, vì chúng sẽ được tham chiếu trong các lệnh AWS CLI sau này.
{{% /notice %}}

#### Tạo Bảng DynamoDB

1. Điều hướng đến dịch vụ **DynamoDB** trong AWS Management Console và chọn **Create table**

2. Cấu hình các thiết lập bảng sau:
   * Table name: `retail-store-ecs-carts`
   * Partition key: `id` (String)
   * Giữ nguyên các thiết lập khác mặc định

![alt text](image.png)
*Hình 2: Thiết lập tạo bảng DynamoDB*

3. Sau khi tạo xong, bạn sẽ thấy trạng thái bảng là "Active"

![alt text](image-1.png)
*Hình 3: Tạo bảng DynamoDB thành công*

#### Thêm Global Secondary Index

1. Trong trang chi tiết bảng `retail-store-ecs-carts`, chọn tab **Indexes** và nhấp vào **Create index**

2. Cấu hình chỉ mục với các thiết lập sau:
   * Partition key: `customerId` (String)
   * Index name: `idx_global_customerId`

![alt text](image-2.png)
*Hình 4: Tạo global secondary index*