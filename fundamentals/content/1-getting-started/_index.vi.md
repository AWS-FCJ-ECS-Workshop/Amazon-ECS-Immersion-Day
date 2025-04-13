---
title : "Bắt đầu"
date :  "`r Sys.Date()`" 
weight : 1
chapter : false
pre: "<b> 1. </b>"
---

#### Ứng dụng mẫu

Hầu hết các bài thực hành trong workshop này sử dụng một ứng dụng mẫu chung với các thành phần được đóng gói dưới dạng container để thực hành. Ứng dụng mô phỏng một cửa hàng web đơn giản, nơi khách hàng có thể duyệt danh mục sản phẩm, thêm sản phẩm vào giỏ hàng và hoàn tất đơn hàng thông qua quy trình thanh toán.

![alt text](/images/1-getting-started/image.png)

Ứng dụng bao gồm một số thành phần và phụ thuộc sau:

![Service Components](/images/1-getting-started/image-1.png)

**Kiến trúc ứng dụng mẫu:**

| Thành phần | Mô tả |
|------------|--------|
| UI | Giao diện người dùng tổng hợp các lệnh gọi API đến các dịch vụ khác |
| Catalog | API cung cấp danh sách và chi tiết sản phẩm |
| Cart | API quản lý giỏ hàng của khách hàng |
| Checkout | API điều phối quy trình thanh toán |
| Orders | API xử lý và tiếp nhận đơn đặt hàng của khách hàng |
| Static Assets | Phục vụ các tệp tĩnh như hình ảnh sản phẩm |

Mã nguồn đầy đủ có sẵn trên [GitHub](https://github.com/aws-containers/retail-store-sample-app).

---

#### Đóng gói các khối lượng công việc container

Trước khi triển khai khối lượng công việc lên Amazon ECS, chúng cần được đóng gói thành container image và đẩy lên container registry. Mặc dù workshop này không đề cập đến các khái niệm cơ bản về container, nhưng các container image được xây dựng sẵn cho ứng dụng mẫu đã có sẵn trong Amazon Elastic Container Registry (ECR) để sử dụng trong các bài thực hành.

Dưới đây là bảng liệt kê các đường dẫn tới ECR repository và Dockerfile tương ứng:

| Thành phần | ECR Repository | Dockerfile |
|------------|----------------|------------|
| UI | [Đường dẫn](https://gallery.ecr.aws/aws-containers/retail-store-sample-ui) | [Dockerfile](https://github.com/aws-containers/retail-store-sample-app/blob/0.8.5/src/ui/Dockerfile) |
| Catalog | [Đường dẫn](https://gallery.ecr.aws/aws-containers/retail-store-sample-catalog) | [Dockerfile](https://github.com/aws-containers/retail-store-sample-app/blob/0.8.5/src/catalog/Dockerfile) |
| Shopping Cart | [Đường dẫn](https://gallery.ecr.aws/aws-containers/retail-store-sample-cart) | [Dockerfile](https://github.com/aws-containers/retail-store-sample-app/blob/0.8.5/src/cart/Dockerfile) |
| Checkout | [Đường dẫn](https://gallery.ecr.aws/aws-containers/retail-store-sample-checkout) | [Dockerfile](https://github.com/aws-containers/retail-store-sample-app/blob/0.8.5/src/checkout/Dockerfile) |
| Orders | [Đường dẫn](https://gallery.ecr.aws/aws-containers/retail-store-sample-orders) | [Dockerfile](https://github.com/aws-containers/retail-store-sample-app/blob/0.8.5/src/orders/Dockerfile) |
| Assets | [Đường dẫn](https://gallery.ecr.aws/aws-containers/retail-store-sample-assets) | [Dockerfile](https://github.com/aws-containers/retail-store-sample-app/blob/0.8.5/src/assets/Dockerfile) |


---

#### Kết quả mong đợi

Phần **Fundamentals** sẽ giúp bạn phát triển hiểu biết vững chắc về các khái niệm chính của Amazon ECS và AWS Fargate, bao gồm cluster, task definition, service, networking và kiến trúc triển khai. Kết thúc phần này, bạn sẽ có thể tự tin triển khai các khối lượng công việc container trên AWS sử dụng ECS và Fargate.

---

#### Tổng quan về giá

{{% notice info %}}
⚠️ Hãy tìm hiểu kỹ cấu trúc giá và các yếu tố chi phí trước khi triển khai để tránh phát sinh chi phí ngoài ý muốn!
{{% /notice %}}

Workshop này sử dụng nhiều dịch vụ AWS như VPC networking, ECS (Fargate launch type) và Application Load Balancer (ALB). Ước tính chi phí dưới đây dựa trên khu vực **Châu Á Thái Bình Dương (Sydney)** và giả định mô hình sử dụng điển hình cho môi trường demo hoặc phát triển.

Bạn có thể xem và tùy chỉnh ước tính này bằng [AWS Pricing Calculator](https://calculator.aws/#/estimate?id=850c71ae0c1cbf130c921383ceb3c5907f83e46c).

| Tên dịch vụ                 | Mô tả                              | Chi phí hàng tháng (USD) | Chi phí 12 tháng (USD) |
|----------------------------|------------------------------------|---------------------------|-------------------------|
| **AWS Fargate**            | Thực thi task container            | $28.81                    | $345.72                 |
| **Application Load Balancer** | Phân phối lưu lượng đến ECS task | $33.00                    | $396.00                 |
| **Data Transfer**          | Lưu lượng internet & giữa các dịch vụ | $0.00                  | $0.00                   |
| **NAT Gateway**            | Cho phép truy cập internet đi ra   | $43.13                    | $517.56                 |
| **Gateway Load Balancer**  | Xử lý lọc lưu lượng nâng cao       | $34.10                    | $409.20                 |
| **Tổng cộng**              |                                    | **$139.04**               | **$1,668.48**           |