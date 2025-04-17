---
title : "Amazon GuardDuty"
date :  "`r Sys.Date()`" 
weight : 4
chapter : false
pre: "<b> 4. </b>"
---

{{% notice info %}}
Hoàn thành chương [Fundamentals](/Amazon-ECS-Immersion-Day/fundamentals/) trước khi tiếp tục với bài thực hành này.
{{% /notice %}}

[Amazon GuardDuty](https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_settingup.html) là dịch vụ giám sát bảo mật liên tục phân tích và xử lý các nguồn dữ liệu để xác định các mối đe dọa đối với tài khoản và khối lượng công việc AWS của bạn. Nó cung cấp khả năng giám sát thời gian chạy được thiết kế đặc biệt cho các ECS cluster và container, phát hiện các mối đe dọa bảo mật tiềm ẩn và các hoạt động trái phép.

Chương này hướng dẫn bạn:

- Kích hoạt và cấu hình GuardDuty cho môi trường AWS của bạn
- Tích hợp GuardDuty với các ECS cluster của bạn
- Giám sát các sự kiện bảo mật liên quan đến ECS bao gồm:
  - Hoạt động API đáng ngờ
  - Các container instance có khả năng bị xâm phạm
  - Giao tiếp với các địa chỉ IP độc hại đã biết
  - Hành vi container bất thường

Sau khi hoàn thành, bạn sẽ triển khai được hệ thống phát hiện mối đe dọa toàn diện cho khối lượng công việc ECS của mình. Bạn sẽ hiểu cách:

- Giám sát các phát hiện của GuardDuty thông qua AWS Management Console
- Diễn giải các cảnh báo bảo mật liên quan đến ứng dụng container của bạn
- Thực hiện các hành động khắc phục phù hợp dựa trên phát hiện của GuardDuty
- Cấu hình phản hồi tự động cho các sự kiện bảo mật

![alt text](/images/4-amazon-guardduty/image.png)
*Hình 1: Amazon GuardDuty giám sát các ECS task để phát hiện mối đe dọa bảo mật*