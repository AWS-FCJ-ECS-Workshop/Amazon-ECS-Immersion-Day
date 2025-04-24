---
title: "Catalog IAM Role"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: "<b> 3. </b>"
---

Trong phần này, chúng ta sẽ tạo Catalog Task Execution Role cho phép dịch vụ Catalog tương tác với các dịch vụ AWS thay mặt bạn.

#### Tạo Catalog Task Execution Role

Điều hướng đến [IAM Role Dashboard](https://console.aws.amazon.com/iam/home/roles) và chọn Create Role

Trong giao diện Select trusted entity:
- Chọn AWS Service
- Chọn use case Elastic Container Service
- Chọn Elastic Container Service Task

![Giao diện Select trusted entity](/images/1-prerequisites/3-iam-role/image.png)
*Hình 1. Giao diện Select trusted entity*

Trong Add Permission, đính kèm `AmazonECSTaskExecutionRolePolicy` > Next

![Giao diện Add Permission](/images/1-prerequisites/3-iam-role/image-1.png)
*Hình 2. Giao diện Add Permission*

Sau khi tạo thành công, truy cập chi tiết role. Trong tab **Permissions**, chọn menu thả xuống > Create Inline Policy

![Chi tiết Catalog Role](/images/1-prerequisites/3-iam-role/image-2.png)
*Hình 3. Chi tiết Catalog Role*

Truy cập Policy Editor > tab JSON và thêm policy sau:

```json
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Effect": "Allow",
            "Action": [
                "ssm:GetParameters",
                "secretsmanager:GetSecretValue"
            ],
            "Resource": "*"
		}
	]
}
```

Thêm policy này thông qua Policy Editor

![Thêm inline policy](/images/1-prerequisites/3-iam-role/image-3.png)
*Hình 4. Thêm inline policy*

Trong bước Review and Create, đặt tên policy là `catalogTaskExecutionPolicy`

![Bước Review and Create](/images/1-prerequisites/3-iam-role/image-4.png)
*Hình 5. Bước Review and Create*

Thêm inline policy thành công:

![Thêm inline policy thành công](/images/1-prerequisites/3-iam-role/image-5.png)
*Hình 6. Thêm inline policy thành công*