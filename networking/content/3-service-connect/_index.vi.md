---
title: "ECS Service Connect"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: "<b> 3. </b>"
---

{{% notice info %}}
Hoàn thành chương [Fundamentals](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/fundamentals/) trước khi tiếp tục với bài thực hành này.
{{% /notice %}}

![alt text](/images/3-service-connect/image.png?width=15pc)
*Hình 1. AWS Cloud Map*

**Amazon ECS Service Connect** cung cấp giải pháp đơn giản hóa cho việc giao tiếp giữa các service trong ứng dụng container của bạn. Nó cho phép khả năng khám phá service, kết nối và giám sát lưu lượng một cách liền mạch. Sử dụng Service Connect, các ứng dụng của bạn có thể giao tiếp thông qua tên service và cổng tiêu chuẩn trên:

- Các service trong cùng một ECS cluster
- Các service ở các cluster khác nhau
- Các service trên các VPC khác nhau trong cùng AWS Region

[Tìm hiểu thêm về Service Connect trong tài liệu AWS.](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/networking-connecting-services.html#networking-connecting-services-serviceconnect)

Amazon ECS cung cấp các tùy chọn bổ sung để cấu hình giao tiếp giữa các service:

* [Internal Application Load Balancer](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/networking-connecting-services.html#networking-connecting-services-elb) - Cho giao tiếp cân bằng tải giữa các service
* [AWS Cloud Map Service Discovery](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/networking-connecting-services.html#networking-connecting-services-direct) - Cho khám phá service dựa trên DNS
* [Amazon VPC Lattice](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-vpc-lattice.html) - Cho mạng ứng dụng trên nhiều VPC và tài khoản