---
title: "Amazon Elastic Container Service (ECS)"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: "<b> 3. </b>"
---

### Amazon ECS Core Components

![alt text](/images/3-fundamentals/image.png)

Amazon ECS (Elastic Container Service) bao gồm 4 thành phần cốt lõi sau:

#### Cluster
- Là một nhóm logic của các services và tasks
- Hoạt động như một môi trường chạy container độc lập
- Có thể chạy trên EC2 instances hoặc serverless với Fargate
- Cho phép quản lý tài nguyên và phân bổ công suất tính toán

#### Service  
- Duy trì và quản lý một nhóm các task giống hệt nhau
- Đảm bảo số lượng task mong muốn luôn được duy trì
- Tích hợp với Application Load Balancer để phân phối traffic
- Hỗ trợ auto-scaling và self-healing

{{< youtube fXiUlXy5kRA >}}


#### Task
- Đại diện cho một hoặc nhiều container thực hiện một chức năng ứng dụng cụ thể
- Là đơn vị nhỏ nhất có thể được triển khai trên ECS
- Chạy độc lập hoặc là một phần của service
- Được định nghĩa bởi task definition

#### Task Definition
- Blueprint cho việc chạy containers trong ECS
- Xác định:
  - Container images 
  - Yêu cầu về CPU và Memory
  - Ports và volumes mapping
  - Cấu hình networking
  - IAM roles
  - Các biến môi trường
  - Logging configuration

{{< youtube 5uJUmGWjRZY >}}

Hiểu rõ về các thành phần này là nền tảng quan trọng để triển khai và vận hành hiệu quả các ứng dụng container hóa trên Amazon ECS. Mỗi thành phần đóng vai trò thiết yếu trong kiến trúc tổng thể của hệ thống container.