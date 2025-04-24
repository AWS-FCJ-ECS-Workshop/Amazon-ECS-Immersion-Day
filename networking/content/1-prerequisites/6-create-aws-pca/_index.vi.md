---
title: "Tạo AWS Private Certificate Authority"
date: "`r Sys.Date()`"
weight: 6
chapter: false
pre: "<b> 6. </b>"
---

#### AWS Private Certificate Authority là gì?

AWS Private Certificate Authority (AWS Private CA) là dịch vụ được quản lý cho phép bạn phát hành và quản lý chứng chỉ riêng một cách an toàn cho các tài nguyên được kết nối từ một vị trí trung tâm.

#### Trường hợp sử dụng

Kích hoạt Transport Layer Security (TLS) cho Application Load Balancer.

#### Tạo AWS Private CA

Truy cập [AWS Private Certificate Authority console](https://console.aws.amazon.com/acm-pca/home/certificateAuthorities)

1. Chọn **Create a private CA**

Cấu hình các thiết lập sau:
- Mode: `Short-lived certificate`
- CA type: `Root`
- Organization: `ECS Immersion Day`
- Organization Unit: `Workshop`
- Country Name: `United States (US)`

![alt text](/images/1-prerequisites/6-create-aws-pca/image.png)
*Hình 1: Tạo AWS Private CA*

{{% notice note %}}
RSA 2048-bit cung cấp bảo mật trực tuyến nâng cao cho ứng dụng của bạn
{{% /notice %}}

- Chọn **RSA 2048**

![alt text](/images/1-prerequisites/6-create-aws-pca/image-1.png)
*Hình 2: Lựa chọn thuật toán khóa*

Thêm tag để quản lý tài nguyên

![alt text](/images/1-prerequisites/6-create-aws-pca/image-2.png)
*Hình 3: Quản lý tag*

Sau khi tạo Private CA thành công:
1. Chọn dropdown **Action**
2. Chọn **Install CA Certificate**

![alt text](/images/1-prerequisites/6-create-aws-pca/image-3.png)
*Hình 4: Xác nhận tạo Private CA*

Trong giao diện Install CA Certificate:
- Chọn thuật toán chữ ký: `SHA256 RSA`

![alt text](/images/1-prerequisites/6-create-aws-pca/image-4.png)
*Hình 5: Cấu hình Install CA Certificate*

Sau khi cài đặt thành công, yêu cầu chứng chỉ:
1. Truy cập [AWS Certificate Manager](https://console.aws.amazon.com/acm/home/welcome)
2. Chọn **Request a Certificate**

![alt text](/images/1-prerequisites/6-create-aws-pca/image-5.png)
*Hình 6: Giao diện AWS Certificate Manager (ACM)*

Trong giao diện Request Certificate:
1. Chọn **Request a private certificate**
2. Nhấp **Next**

![alt text](/images/1-prerequisites/6-create-aws-pca/image-6.png)
*Hình 7: Lựa chọn yêu cầu chứng chỉ*

Cấu hình chứng chỉ riêng:
1. Chọn CA vừa tạo
2. Nhập tên miền
3. Chọn thuật toán khóa: `RSA 2048`

![alt text](/images/1-prerequisites/6-create-aws-pca/image-7.png)
*Hình 8: Cấu hình chứng chỉ riêng*

Xác nhận yêu cầu chứng chỉ

![alt text](/images/1-prerequisites/6-create-aws-pca/image-8.png)
*Hình 9: Yêu cầu chứng chỉ riêng thành công*

Thiết lập biến môi trường:

```bash
export ALB_CERTIFICATE_ARN=arn:aws:acm:ap-southeast-2:XXXXXXXXXXX:certificate/1c018d88-4404-48f5-8585-d460ed8c22ba
```