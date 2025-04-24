---
title: "Cloud Map - Service Discovery"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: "<b> 2. </b>"
---

![Cloud map icon](/images/3-service-connect/image.png?width=10pc)

*Hình 1. Biểu tượng Cloud Map*

### AWS Cloud Map là gì?

AWS Cloud Map là giải pháp service discovery được quản lý hoàn toàn cho phép bạn ánh xạ tên logic với các dịch vụ và tài nguyên backend của ứng dụng. Nó giúp ứng dụng của bạn định vị các phụ thuộc thông qua một service registry thống nhất.

### Các thành phần của AWS Cloud Map

#### Namespace

Namespace cung cấp phạm vi duy nhất cho tài nguyên của bạn và xác định cách bạn muốn khám phá chúng. Nó hoạt động như một container cho các dịch vụ của bạn và xác định phương thức khám phá - thông qua truy vấn DNS, lệnh gọi API, hoặc cả hai.

#### Service 

Sau khi tạo namespace, bạn xác định các service để đại diện cho mỗi loại tài nguyên bạn muốn khám phá. Ví dụ, bạn có thể tạo các service riêng biệt cho máy chủ web, máy chủ ứng dụng và máy chủ cơ sở dữ liệu.

#### Service Instance

Service instance là các tài nguyên thực tế được đăng ký với AWS Cloud Map. Khi ứng dụng của bạn thêm một tài nguyên, nó gọi API RegisterInstance để tạo một service instance trong một service. Điều này cho phép các ứng dụng khác khám phá và kết nối với các tài nguyên này.

### Trường hợp sử dụng trong Workshop

Trong workshop này, chúng ta sẽ sử dụng AWS Cloud Map để triển khai service discovery cho ba microservice: dịch vụ UI, dịch vụ catalog và dịch vụ assets. Điều này thể hiện khả năng service discovery của Cloud Map trong kiến trúc microservices.

#### Tạo Namespace Service Discovery

1. Điều hướng đến [AWS Cloud Map Namespaces console](https://console.aws.amazon.com/cloudmap/home/namespaces) và chọn **Create Namespace**

![Cloud Map Namespaces Console](/images/1-prerequisites/2-service-discovery/image.png)

*Hình 2. Giao diện Cloud Map Namespaces*

2. Cấu hình namespace với các cài đặt sau:
   - Tên namespace: `retailstore.local`
   - Instance discovery: `API calls and DNS Queries in VPCs`
   - VPC: `ecs-lab-vpc`

![Create Namespace Configuration](/images/1-prerequisites/2-service-discovery/image-1.png)
*Hình 3. Cấu hình Tạo Namespace*

3. Xác nhận việc tạo namespace thành công:

![Successful Namespace Creation](/images/1-prerequisites/2-service-discovery/image-2.png)
*Hình 4. Tạo Namespace Thành Công*

✅ Bạn đã tạo thành công một namespace Cloud Map