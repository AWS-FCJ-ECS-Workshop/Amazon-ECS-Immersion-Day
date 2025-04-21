---
title: "Catalog IAM Role"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: "<b> 3. </b>"
---

<!-- 3. cataologEcsTaskExecutionRole  -->
Trong section này, chúng ta sẽ tiến hành tạo Catalog Task Execution Role, mục đích gán cho Catalog service giúp Catalog gọi các AWS services thay bạn.

#### Tạo Catalog Task Execution Role

Truy cập [IAM Role Dashboard](https://console.aws.amazon.com/iam/home/roles) > Create Role

Trong giao diện Select trusted entity:
- Chọn AWS Service
- Elastic Container Service use case
- Elastic Container Service Task

![Giao diện Select trusted entity](image.png)
*Figure 1. Giao diện Select trusted entity*

Tại Add Permission, gán policy `AmazonECSTaskExecutionRolePolicy` > Next

![Add Permission](image-1.png)
*Figure 2. Giao diện Add Permission*

Khi tạo thành công, truy cập detail role, tại **Permission* tab chọn dropdown > Create Inline Policy

![alt text](image-2.png)
*Figure 3. Detail Catalog Role*

Truy cập Policy Editor > tab JSON

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

Thêm policy này vào thông qua Policy Editor

![alt text](image-3.png)
*Figure 4. Thêm inline policy*

Tại bước Review and Create, đặt tên policy name `catalogTaskExecutionPolicy`

![alt text](image-4.png)
*Figure 5. Review and Create step*

Thêm Inline policy thành công:

![alt text](image-5.png)
*Figure 6. Thêm inline policy thành công*