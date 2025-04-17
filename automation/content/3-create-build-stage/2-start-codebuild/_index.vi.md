+++
title = "Start Build Project"
date = 2020-05-14T00:38:32+07:00
weight = 3
chapter = false
pre = "<b>3.2 </b>"
+++

1.  Sau khi tạo CodeBuild thành công

    - Chọn **Start build**

    ![CodeBuild-24](/images/3/3.1-24.png?width=90pc)
    ![CodeBuild-22](/images/3/3.1-22.png?width=90pc)
    ![CodeBuild-25](/images/3/3.1-25.png?width=90pc)

2.  Kiểm tra ECR Repository

    - Truy cập [Amazon Elastic Container Registry (Amazon ECR)](https://ap-northeast-2.console.aws.amazon.com/ecr)
    - Chọn **Repositories**.
    - Chọn Repository đã tạo
      ![CodeBuild-26](/images/3/3.1-28.png?width=90pc)

    Bạn sẽ thấy Images đã được cập nhật.

    ![CodeBuild-27](/images/3/3.1-26.png?width=90pc)

3.  Kiểm tra S3 Bucket

    - Truy cập [Amazon Simple Storage Service (Amazon S3)](https://ap-northeast-2.console.aws.amazon.com/s3)
    - Chọn Bucket đã tạo
      ![CodeBuild-29](/images/3/3.1-29.png?width=90pc)

    Bạn sẽ thấy Objects đã được cập nhật.

    ![CodeBuild-30](/images/3/3.1-27.png?width=90pc)

{{% notice info %}}
**Khắc phục sự cố**
{{% /notice %}}
Nếu bạn gặp lỗi trong quá trình build, hãy kiểm tra tệp Buildspec bằng cách:

- Chọn **Edit** project
- Cuộn xuống và chỉnh sửa các lệnh trong **Buildspec**.
