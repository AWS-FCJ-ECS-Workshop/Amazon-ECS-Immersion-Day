---
title: "Điều kiện tiên quyết"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: "<b> 2. </b>"
---

Trước khi triển khai ứng dụng trên Amazon ECS và AWS Fargate, chúng ta cần xây dựng một môi trường hạ tầng cơ bản, bao gồm thiết lập quyền truy cập, cấu hình mạng, bảo mật và cân bằng tải. Mục tiêu là đảm bảo môi trường đủ điều kiện để chạy các container một cách an toàn, ổn định và có khả năng mở rộng.

![ECS Networking Architecture](/images/2-prerequisites/ECS-Lab-Networking.png)

---

#### 1. IAM Roles – Phân quyền truy cập dịch vụ

**IAM Role** là cơ chế cấp quyền tạm thời cho các dịch vụ AWS truy cập tài nguyên mà không cần sử dụng thông tin đăng nhập cố định.

Trong workshop này, bạn sẽ tạo:

- **EC2 Instance Role**: Cấp quyền cho EC2 truy cập dịch vụ như CloudWatch hoặc S3.
- **ECS Task Execution Role**: Cho phép ECS tải container image từ Amazon ECR và ghi log lên CloudWatch.
- **ECS Service Role**: Cần thiết khi ECS tích hợp với các dịch vụ như Application Load Balancer để tự động đăng ký/dỡ đăng ký các task.

{{% notice note %}}
Security Best Practices: Cung cấp đủ Policy, đảm bảo các thành phần ECS có đầy đủ quyền để vận hành chính xác và bảo mật.
{{% /notice %}}

---

#### 2. Networking – Cấu hình mạng ảo

Bạn sẽ cấu hình một mạng riêng biệt cho ứng dụng container với các thành phần:

- **VPC (Virtual Private Cloud)**: Mạng ảo tùy chỉnh, cung cấp không gian địa chỉ IP riêng biệt.
- **Subnets**: Chia mạng thành các vùng con như public subnet (truy cập Internet) và private subnet (chỉ nội bộ).
- **Route Table**: Định tuyến lưu lượng giữa các subnet và ra ngoài Internet.
- **Internet Gateway**: Cổng kết nối public subnet với Internet.
- **Availability Zones (AZs)**: Triển khai dịch vụ trên nhiều AZ để đảm bảo khả năng chịu lỗi cao.
- **NAT Gateway**: Cho phép các instance bên trong private subnet có thể truy cập Internet.


{{% notice note %}}
Cung cấp môi trường mạng có độ sẵn sàng cao và bảo mật để triển khai container trong kiến trúc phân tán.
{{% /notice %}}

---

#### 3. Security Group – Tường lửa cấp tài nguyên

**Security Group** đóng vai trò như một firewall, kiểm soát lưu lượng vào/ra của các tài nguyên trong VPC.

- Cấu hình để cho phép các port cần thiết (HTTP - 80, HTTPS - 443, hoặc cổng nội bộ).
- Chỉ định rõ IP nguồn hoặc dịch vụ được phép truy cập.

{{% notice note %}}
Đảm bảo ứng dụng container được bảo vệ khỏi các truy cập trái phép trong môi trường production.
{{% /notice %}}


---

#### 4. Load Balancer - Cân bằng tải

- **Application Load Balancer (ALB)** giúp phân phối lưu lượng HTTP/HTTPS đến các ECS Tasks.
- Tạo **Target Group** để quản lý các ECS Task là backend target.
- Thiết lập **Listener Rules** để định tuyến lưu lượng đến đúng target group dựa trên path hoặc host.

{{% notice note %}}
Đảm bảo khả năng mở rộng khi phân tán incoming traffic đều giữa các UI Services.
{{% /notice %}}