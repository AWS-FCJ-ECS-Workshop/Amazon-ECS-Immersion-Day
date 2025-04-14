---
title : "Giới thiệu"
date :  "`r Sys.Date()`" 
weight : 1
chapter : false
pre: "<b> 1. </b>"
---

#### Horizontal Scaling là gì?

Horizontal scaling là chiến lược tăng năng lực hệ thống bằng cách thêm các instance tính toán để xử lý nhu cầu tải tăng cao. Trong Amazon Elastic Container Service (Amazon ECS), các microservice tận dụng [Application Auto Scaling](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/service-auto-scaling.html) để tự động điều chỉnh năng lực. Các quyết định scaling được dựa trên các chỉ số thời gian thực từ [giám sát mức sử dụng dịch vụ Amazon ECS](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/cloudwatch-metrics.html#service_utilization), theo dõi các chỉ số hiệu năng chính như mức sử dụng CPU và tiêu thụ bộ nhớ.

![Horizontal scaling](/images/1-introduction/image.png)
*Hình 1: Kiến trúc horizontal scaling trong Amazon ECS*

#### Cân nhắc về Chi phí

Khi triển khai horizontal scaling với AWS Auto Scaling, bạn không phải trả thêm phí cho chức năng scaling. Chi phí của bạn chỉ giới hạn ở:
- Tài nguyên AWS được sử dụng bởi các ứng dụng đang chạy
- Phí giám sát và metrics của Amazon CloudWatch

{{% notice note %}}
Workshop này tuân theo mô hình giá được mô tả trong chương [Fundamentals](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/fundamentals/). Các dịch vụ được cấu hình để tự động scale lên tối đa 10 task. Dự tính chi phí dưới đây phản ánh cấu hình scaling này.
{{% /notice %}}

#### Chi phí Ước tính

| Dịch vụ | Mục đích | Chi phí Hàng tháng (USD) | Chi phí Hàng năm (USD) |
|---------|----------|-------------------|-------------------|
| **AWS Fargate** | Thực thi container task | $9.01 | $108.12 |

Những ước tính chi phí này cung cấp hiểu biết cơ bản về tác động tài chính khi triển khai horizontal scaling với Amazon ECS và AWS Fargate. Chi phí thực tế của bạn sẽ thay đổi dựa trên các yếu tố như thời gian thực thi task, mức sử dụng tài nguyên và mô hình scaling.