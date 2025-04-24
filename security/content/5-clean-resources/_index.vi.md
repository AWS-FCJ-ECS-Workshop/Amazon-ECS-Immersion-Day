---
title : "Dọn dẹp tài nguyên"
date :  "`r Sys.Date()`" 
weight : 5
chapter : false
pre: "<b> 5. </b>"
---

{{% notice info %}}
Quan trọng: Phần Clean Up Resources này sẽ chỉ xóa các dịch vụ được tạo trong phần này.
Để xóa tất cả tài nguyên, bạn phải xóa chúng theo thứ tự bắt đầu từ phần [Fundamentals](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/fundamentals/).
{{% /notice %}}

Để đảm bảo bạn không phải chịu các khoản phí không cần thiết, hãy làm theo các bước sau để dọn dẹp tài nguyên đã tạo trong workshop này:

1. Xóa Cart Security Group
   * Điều hướng đến EC2 Console > Security Groups
   * Chọn security group có tên `ecs-lab-cart-sg`
   * Chọn **Delete security group** từ menu Actions
   * Xác nhận xóa

![Delete Security Group](/images/5-clean-resources/image.png)

2. Xóa DynamoDB Table
   * Truy cập DynamoDB Console
   * Chọn bảng đã tạo cho workshop này
   * Chọn **Delete table**
   * Nhập "delete" để xác nhận
   * Đợi cho đến khi bảng được xóa hoàn toàn

![Delete DynamoDB Table](/images/5-clean-resources/image-1.png)

3. Xóa Cart Services
   * Truy cập ECS Console
   * Chọn cart service cluster
   * Chọn cart service
   * Chọn **Delete**
   * Đợi cho đến khi service và tasks được kết thúc

![Delete Cart Services](/images/5-clean-resources/image-2.png)

4. Tắt Amazon GuardDuty
   * Điều hướng đến GuardDuty Console
   * Chọn **Settings**
   * Chọn **Disable GuardDuty**
   * Xác nhận hành động
   * Đợi cho đến khi GuardDuty được tắt hoàn toàn

![Disable GuardDuty](/images/5-clean-resources/image-3.png)

**Quan trọng**: Xác minh tất cả tài nguyên đã được xóa đúng cách để tránh phát sinh phí. Kiểm tra AWS Console của bạn để đảm bảo không còn tài nguyên liên quan nào đang hoạt động.
