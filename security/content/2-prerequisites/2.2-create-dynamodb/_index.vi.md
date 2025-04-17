---
title: "Tạo DynamoDB"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: "<b> 2.2. </b>"
---

Tạo bảng DynamoDB để lưu trữ thông tin giỏ hàng cho ứng dụng cửa hàng bán lẻ.

{{% notice info %}}
Tên bảng và index phải khớp chính xác như được chỉ định, vì chúng sẽ được tham chiếu trong các lệnh AWS CLI tiếp theo.
{{% /notice %}}

#### Tạo bảng DynamoDB

1. Truy cập AWS Management Console và điều hướng đến dịch vụ **DynamoDB**

2. Chọn **Create table** và nhập các thông tin cấu hình sau:
   * Table name: `retail-store-ecs-carts`
   * Partition key: `id` (String) 
   * Giữ tất cả các cài đặt khác ở giá trị mặc định

![alt text](/images/2-prerequisites/2.2-create-dynamodb/image.png)
*Hình 2: Cấu hình bảng DynamoDB*

3. Đợi quá trình tạo bảng hoàn tất. Trạng thái bảng sẽ hiển thị là "Active" khi sẵn sàng

![alt text](/images/2-prerequisites/2.2-create-dynamodb/image-1.png)
*Hình 3: Bảng DynamoDB đã được tạo thành công*

#### Thêm Global Secondary Index

1. Trên trang chi tiết bảng `retail-store-ecs-carts`, điều hướng đến tab **Indexes** và chọn **Create index**

2. Thiết lập index với các tham số sau:
   * Partition key: `customerId` (String)
   * Index name: `idx_global_customerId`

![alt text](/images/2-prerequisites/2.2-create-dynamodb/image-2.png)
*Hình 4: Cấu hình Global secondary index*