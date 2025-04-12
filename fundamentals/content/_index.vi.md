---
title : "Hands-on Workshop: Amazon Elastic Container Services Fundamentals"
date :  "`r Sys.Date()`" 
chapter : false
---

# Series of Amazon Elastic Container Services

Chào mừng bạn đến với chuỗi workshop về Amazon Elastic Container Service!

Chuỗi workshop này bao gồm **7 bài thực hành** giúp bạn tìm hiểu toàn diện về Amazon Elastic Container Services.

1. [Fundamentals](#) - Xây dựng kiến thức nền tảng về Network, triển khai ứng dụng mẫu và khởi tạo ECS Services
1. [Auto Scaling](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/auto-scaling) - Tự động điều chỉnh quy mô ứng dụng
2. [Networking](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/networking) - Thiết kế và triển khai mô hình mạng
3. [Observability](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/observability) - Giám sát và quan sát hệ thống
4. [Security](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/security) - Bảo mật và kiểm soát truy cập
5. [Automation](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/automation) - Tự động hóa quy trình triển khai

![alt text](/images/image.png)

#### Amazon Elastic Container Service là gì?

**Amazon Elastic Container Service (ECS)** là dịch vụ điều phối container được quản lý toàn diện của AWS. Dịch vụ này cho phép bạn triển khai, quản lý và mở rộng quy mô các ứng dụng container một cách hiệu quả. ECS tích hợp chặt chẽ với hệ sinh thái AWS, cung cấp giải pháp đơn giản để vận hành các workload container trên môi trường đám mây và tại chỗ, đi kèm tính năng bảo mật nâng cao thông qua Amazon ECS Anywhere.

**AWS Fargate** là công nghệ điện toán serverless theo mô hình trả tiền theo mức sử dụng. Fargate cho phép bạn tập trung vào phát triển ứng dụng mà không cần quản lý cơ sở hạ tầng. Bằng cách chuyển giao các tác vụ quản lý máy chủ, phân bổ tài nguyên và điều chỉnh quy mô cho AWS, bạn không chỉ tối ưu hóa vận hành mà còn đẩy nhanh quá trình phát triển sản phẩm trên đám mây, đồng thời tối ưu chi phí tổng thể.

#### Bạn sẽ học được gì?
Workshop này được thiết kế theo module giúp bạn khám phá các chủ đề quan trọng về Amazon ECS:

1. Nắm vững nền tảng của **Amazon ECS và Fargate**, bao gồm clusters, tasks và services
2. Triển khai hệ thống **auto scaling** để tự động điều chỉnh theo tải
3. Xây dựng hệ thống **giám sát toàn diện** thông qua logs, metrics và traces
4. Tìm hiểu sâu về mạng AWS Fargate và **kiến trúc mạng nâng cao**
5. Triển khai **giải pháp bảo mật** toàn diện, bao gồm quản lý thông tin xác thực và secrets
6. Xây dựng quy trình **tự động hóa triển khai** trên Amazon ECS sử dụng CI/CD pipelines

#### Đối tượng mục tiêu

Workshop cấp độ **200+** này được thiết kế dành cho:
- Kỹ sư phát triển phần mềm
- Kỹ sư nền tảng
- Chuyên gia công nghệ thông tin

Phù hợp với những người muốn có kinh nghiệm thực tế về Amazon ECS, từ các thành phần cơ bản đến các tính năng nâng cao trong việc triển khai và quản lý workload container.

#### Yêu cầu tiên quyết
Workshop này không phải là khóa học nhập môn về containers. Người tham gia cần có kiến thức nền tảng về:

1. [Hiểu biết về containers](https://www.docker.com/resources/what-container/), bao gồm khả năng tạo containers bằng Dockerfile
2. Kinh nghiệm sử dụng AWS Management Console và/hoặc AWS APIs
3. Kinh nghiệm làm việc với Visual Studio Code (hoặc IDE tương đương)

#### Các khái niệm chính

1. [Bắt đầu](1-getting-started/)
2. [Điều kiện tiên quyết](2-prerequisites/) 
3. [Kiến thức nền tảng](3-fundamentals/)
4. [Dọn dẹp tài nguyên](4-clean-resources/)