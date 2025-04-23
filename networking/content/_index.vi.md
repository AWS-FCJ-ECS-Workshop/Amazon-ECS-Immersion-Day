---
title : "Amazon ECS Series: Networking"
date :  "`r Sys.Date()`" 
chapter : false
---

# Networking

Các ứng dụng microservices dựa trên container bao gồm nhiều thành phần phân tán cần giao tiếp với nhau. Trong ứng dụng mẫu của chúng ta, thành phần giao diện người dùng giao tiếp với dịch vụ Catalog thông qua các API, sau đó kết nối với cơ sở dữ liệu MySQL để lưu trữ dữ liệu, như minh họa trong sơ đồ kiến trúc dưới đây.

![ECS Microservices Architecture](/images/image.png)
*Hình 1. Kiến trúc ECS Microservices*

Chương này khám phá các khái niệm mạng thiết yếu của Amazon ECS dành riêng cho AWS Fargate. Việc hiểu rõ những nguyên tắc cơ bản về mạng này rất quan trọng để thiết kế và triển khai hiệu quả các ứng dụng container trên AWS.

Tìm hiểu về:

*   [📘 Prerequisites](1-prerequisites/)
*   [📘 Amazon ECS Network Mode](2-network-mode/)
*   [📘 ECS Service Connect](3-service-connect/)
*   [📘 Amazon ECS Service Connect with TLS](4-service-connect-with-tls/)

Các phần này sẽ hướng dẫn bạn về các thành phần và cấu hình mạng chính cần thiết cho việc giao tiếp container mạnh mẽ trong Amazon ECS với AWS Fargate.