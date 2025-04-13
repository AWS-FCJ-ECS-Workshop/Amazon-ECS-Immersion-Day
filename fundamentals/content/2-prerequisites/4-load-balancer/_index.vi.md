---
title: "Load Balancer - Cân bằng tải"
date: "`r Sys.Date()`"
weight: 4
chapter: false
pre: "<b> 2.4. </b>"
---

### Cân bằng tải là gì?

Cân bằng tải (Load Balancing) là một phương pháp phân phối lưu lượng mạng một cách đồng đều đến một nhóm tài nguyên phục vụ ứng dụng. Trong thời đại số hóa hiện nay, các ứng dụng cần phải đáp ứng hàng triệu người dùng đồng thời và phân phối nhiều loại nội dung như văn bản, video, hình ảnh một cách nhanh chóng và đáng tin cậy. 

Để xử lý khối lượng truy cập lớn, các ứng dụng thường được triển khai trên nhiều máy chủ với dữ liệu được sao chép. Bộ cân bằng tải đóng vai trò trung gian giữa người dùng và nhóm máy chủ, đảm bảo việc phân phối tải một cách công bằng và hiệu quả đến tất cả các máy chủ.

![Load balancer](/images/2-prerequisites/4-load-balancer/ECS-Lab-Networking-LoadBalancer.png)

### Các loại cân bằng tải

AWS cung cấp các loại cân bằng tải sau dựa trên cách thức kiểm tra và điều hướng lưu lượng:

#### 1. Application Load Balancer (ALB)
- Hoạt động ở tầng 7 (Layer 7) của mô hình OSI
- Phân tích nội dung yêu cầu như HTTP headers hoặc SSL session ID
- Phù hợp với các ứng dụng web phức tạp có nhiều thành phần
- Hỗ trợ định tuyến thông minh dựa trên nội dung
#### 2. Network Load Balancer (NLB)
- Hoạt động ở tầng 4 (Layer 4) của mô hình OSI  
- Xử lý lưu lượng dựa trên thông tin mạng (IP, port)
- Cung cấp địa chỉ IP tĩnh cho các máy chủ
- Phù hợp với các ứng dụng yêu cầu hiệu năng cao
#### 3. Global Server Load Balancer
- Phân phối lưu lượng trên nhiều vùng địa lý
- Định tuyến yêu cầu đến máy chủ gần nhất với người dùng
- Hỗ trợ chuyển đổi dự phòng giữa các vùng
- Tối ưu hóa trải nghiệm người dùng toàn cầu
#### 4. DNS Load Balancer
- Cân bằng tải thông qua hệ thống DNS
- Định tuyến yêu cầu qua nhóm tài nguyên trên một domain
- Hỗ trợ phân phối lưu lượng toàn cầu
- Đảm bảo tính sẵn sàng cao cho ứng dụng
### Thực hành tạo Load Balancer

Trong phần thực hành này, chúng ta sẽ tạo Application Load Balancer để phân phối lưu lượng truy cập cho ứng dụng.

#### 1. Tạo ALB Target Group

Target Group là thành phần định nghĩa các đích đến (targets) mà Load Balancer sẽ chuyển tiếp lưu lượng đến. Targets có thể là EC2 instances, IP addresses hoặc Lambda functions.

#### Các bước thực hiện:

1. Truy cập [Target Group Dashboard](console.aws.amazon.com/ec2/home#TargetGroups)
2. Cấu hình Target Group:
   - Target type: IP Addresses
   - Tên: `ui-application`
   - Port: HTTP 8080
   - VPC: `ecs-lab-vpc`
   - Health check path: `/actuator/health` (Path này từ application)

![Target Group Configuration](/images/2-prerequisites/4-load-balancer/image-1.png)

![Health Check Configuration](/images/2-prerequisites/4-load-balancer/image-2.png)

![Target Group Creation Success](/images/2-prerequisites/4-load-balancer/image-7.png)

#### 2. Tạo Application Load Balancer

1. Truy cập [Load Balancer Dashboard](console.aws.amazon.com/ec2/home#LoadBalancers)
2. Chọn Application Load Balancer

![Select ALB Type](/images/2-prerequisites/4-load-balancer/image-3.png)

3. Cấu hình cơ bản:
   - Đặt tên cho Load Balancer
   - Scheme: Internet-facing

![ALB Basic Configuration](/images/2-prerequisites/4-load-balancer/image-5.png)

   - Network: Chọn VPC và public subnets
   - Security Group: `ecs-lab-public-sg` (cho phép inbound HTTP 80 & HTTPS 443)

![Network Configuration](/images/2-prerequisites/4-load-balancer/image-6.png)

   - Listener: HTTP 80 forward đến target group `ui-application`
![Listener Configuration](/images/2-prerequisites/4-load-balancer/image-8.png)

![ALB Creation Success](/images/2-prerequisites/4-load-balancer/image-12.png)

### Kết luận

Trong phần này, bạn đã học cách:
- Tạo Target Group để định nghĩa các đích đến cho lưu lượng
- Thiết lập Application Load Balancer để phân phối lưu lượng truy cập
- Cấu hình các thành phần cần thiết như security group và listeners