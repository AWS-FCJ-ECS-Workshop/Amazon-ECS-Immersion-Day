---
title : "Bắt đầu"
date :  "`r Sys.Date()`" 
weight : 1
chapter : false
pre: "<b> 1. </b>"
---

#### Ứng dụng mẫu

Workshop này sử dụng một ứng dụng bán lẻ mẫu được container hóa để thực hành. Ứng dụng mô phỏng một cửa hàng thương mại điện tử cơ bản nơi khách hàng có thể duyệt sản phẩm, thêm mặt hàng vào giỏ và hoàn tất mua hàng thông qua quy trình thanh toán.

![alt text](/images/1-getting-started/image.png)

Ứng dụng bao gồm các thành phần và phụ thuộc sau:

![Service Components](/images/1-getting-started/image-1.png)

**Kiến trúc ứng dụng mẫu:**

| Thành phần | Mô tả |
|-----------|-------------|
| UI | Giao diện frontend tổng hợp các lệnh gọi API đến backend services |
| Catalog | API cung cấp danh sách và chi tiết sản phẩm |
| Cart | API quản lý giỏ hàng của khách hàng |
| Checkout | API điều phối quy trình thanh toán |
| Orders | API xử lý và nhận đơn hàng của khách hàng |
| Static Assets | Phục vụ các file tĩnh như hình ảnh sản phẩm |

Mã nguồn hoàn chỉnh có sẵn trên [GitHub](https://github.com/aws-containers/retail-store-sample-app).

#### Container hóa các Workload

Trước khi triển khai workload lên Amazon ECS, chúng phải được đóng gói thành container image và đẩy lên container registry trong các bước tiếp theo.

Bảng sau liệt kê đường dẫn ECR repository và Dockerfile tương ứng:

| Thành phần | ECR Repository | Dockerfile |
|-----------|---------------|------------|
| UI | [Link](https://gallery.ecr.aws/aws-containers/retail-store-sample-ui) | [Dockerfile](https://github.com/aws-containers/retail-store-sample-app/blob/0.8.5/src/ui/Dockerfile) |
| Catalog | [Link](https://gallery.ecr.aws/aws-containers/retail-store-sample-catalog) | [Dockerfile](https://github.com/aws-containers/retail-store-sample-app/blob/0.8.5/src/catalog/Dockerfile) |
| Shopping Cart | [Link](https://gallery.ecr.aws/aws-containers/retail-store-sample-cart) | [Dockerfile](https://github.com/aws-containers/retail-store-sample-app/blob/0.8.5/src/cart/Dockerfile) |
| Checkout | [Link](https://gallery.ecr.aws/aws-containers/retail-store-sample-checkout) | [Dockerfile](https://github.com/aws-containers/retail-store-sample-app/blob/0.8.5/src/checkout/Dockerfile) |
| Orders | [Link](https://gallery.ecr.aws/aws-containers/retail-store-sample-orders) | [Dockerfile](https://github.com/aws-containers/retail-store-sample-app/blob/0.8.5/src/orders/Dockerfile) |
| Assets | [Link](https://gallery.ecr.aws/aws-containers/retail-store-sample-assets) | [Dockerfile](https://github.com/aws-containers/retail-store-sample-app/blob/0.8.5/src/assets/Dockerfile) |

#### Kết quả mong đợi

Phần **Fundamentals** sẽ giúp bạn phát triển hiểu biết vững chắc về các khái niệm cốt lõi của Amazon ECS và AWS Fargate, bao gồm clusters, task definitions, services, networking và kiến trúc triển khai. Sau khi hoàn thành, bạn sẽ có thể tự tin triển khai các workload được container hóa trên AWS sử dụng ECS và Fargate.

#### Tổng quan về giá

{{% notice info %}}
⚠️ Hãy xem xét kỹ cấu trúc giá và các yếu tố chi phí trước khi triển khai để tránh các khoản phí không mong muốn!
{{% /notice %}}

Workshop này sử dụng nhiều dịch vụ AWS bao gồm VPC networking, ECS (Fargate launch type) và Application Load Balancer (ALB). Ước tính chi phí dưới đây dựa trên khu vực **Asia Pacific (Sydney)** và giả định các mẫu sử dụng điển hình cho môi trường demo hoặc phát triển.

Bạn có thể xem và tùy chỉnh ước tính này bằng [AWS Pricing Calculator](https://calculator.aws/#/estimate?id=850c71ae0c1cbf130c921383ceb3c5907f83e46c).

| Tên dịch vụ | Mô tả | Chi phí hàng tháng (USD) | Chi phí 12 tháng (USD) |
|--------------|-------------|-------------------|-------------------|
| **AWS Fargate** | Thực thi container task | $1.80 | $21.60 |
| **Application Load Balancer** | Phân phối lưu lượng đến ECS tasks | $33.00 | $396.00 |
| **Data Transfer** | Lưu lượng Internet & giữa các dịch vụ | $0.00 | $0.00 |
| **NAT Gateway** | Cho phép truy cập internet đi ra | $43.13 | $517.56 |
| **Gateway Load Balancer** | Lọc lưu lượng nâng cao | $34.10 | $409.20 |
| **Tổng** | | **$139.04** | **$1,668.48** |