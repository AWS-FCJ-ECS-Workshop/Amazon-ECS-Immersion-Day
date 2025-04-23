---
title: "Tạo Parameters trong Secret Manager"
date: "`r Sys.Date()`"
weight: 5
chapter: false
pre: "<b> 5. </b>"
---

#### AWS Systems Manager
AWS Systems Manager cho phép bạn quản lý tài nguyên AWS ở quy mô lớn trên các môi trường AWS, hybrid và multi-cloud.

#### AWS Secrets Manager
AWS Secrets Manager giúp bạn bảo vệ quyền truy cập vào ứng dụng, dịch vụ và tài nguyên IT của mình. Dịch vụ này cho phép bạn dễ dàng xoay vòng, quản lý và truy xuất thông tin đăng nhập cơ sở dữ liệu, khóa API và các bí mật khác trong suốt vòng đời của chúng.

#### Tạo Database Endpoint Parameter

Sau khi tạo thành công instance cơ sở dữ liệu, truy cập chi tiết instance và sao chép endpoint:

![alt text](/images/1-prerequisites/5-secret-manager/image.png)
*Hình 1: Chi tiết instance cơ sở dữ liệu được tạo trong phần 4*

```bash
# Thay thế --value bằng endpoint của bạn
aws ssm put-parameter \
  --name "/retail-store-ecs/catalog/db-endpoint" \
  --value "<your-mysql-url>:3306" \
  --type "String" \
  --overwrite
  
aws ssm get-parameter --name "/retail-store-ecs/catalog/db-endpoint"
```

#### Tạo Secret Values

{{% notice info %}}
Lưu ý: Thay thế `username` & `password` nếu bạn đã sử dụng các giá trị khác khi tạo cơ sở dữ liệu.
{{% /notice %}}

```bash
aws secretsmanager create-secret \
  --name "retail-store-ecs-catalog-db" \
  --secret-string '{"username":"admin","password":"admin123"}'
  
aws secretsmanager get-secret-value --secret-id "retail-store-ecs-catalog-db"
```

![alt text](/images/1-prerequisites/5-secret-manager/image-1.png)
*Hình 2: Đã tạo thành công các giá trị trong AWS Secrets Manager*