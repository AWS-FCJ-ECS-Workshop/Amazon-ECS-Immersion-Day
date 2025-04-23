+++
title = "Dọn dẹp tài nguyên"
date = 2024-05-14T00:38:32+07:00
weight = 6
chapter = false
pre = "<b>6. </b>"
+++

Chúng ta sẽ tiếp tục với các bước sau để xóa các tài nguyên mà chúng ta đã tạo trong bài lab này.

### Xóa CodePipeline

Trong phần Pipelines:

- Chọn Pipeline đã tạo
- Chọn **Delete pipeline**
  ![Delete-Pipeline-1](/images/6/6-1.png?width=90pc)

- Nhập `delete` để xác nhận việc xóa
- Chọn **Delete**
  ![Delete-Pipeline-2](/images/6/6-2.png?width=90pc)

### Xóa CodeBuild

Trong phần Build projects:

- Chọn project đã tạo
- Chọn **Actions**
- Chọn **Delete**
  ![Delete-Pipeline-3](/images/6/6-3.png?width=90pc)

- Chọn `delete` để xác nhận việc xóa
- Chọn **Delete**
  ![Delete-Pipeline-4](/images/6/6-4.png?width=90pc)

### Delete S3

Trong phần S3 Bucket

- Chọn bucket đã tạo
- Chọn **Empty**
  ![Delete-S3](/images/6/6-5.png?width=90pc)
- Nhập `permanently delete` để xác nhận việc xóa
  ![Delete-S3](/images/6/6-6.png?width=90pc)
- Tiếp tục chọn bucket đã tạo
- Chọn **Delete**
  ![Delete-S3](/images/6/6-7.png?width=90pc)
- Nhập tên của bucket để xác nhận việc xóa
  ![Delete-S3](/images/6/6-8.png?width=90pc)

{{% notice info %}}
Các tài nguyên còn lại sẽ được sử dụng cho bài lab tiếp theo.
{{% /notice %}}
