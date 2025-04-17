+++
title = "Start Build Project"
date = 2020-05-14T00:38:32+07:00
weight = 3
chapter = false
pre = "<b>3.2 </b>"
+++

1.  After successfully creating CodeBuild

    - Select **Start build**

    ![CodeBuild-24](/images/3/3.1-24.png?width=90pc)
    ![CodeBuild-22](/images/3/3.1-22.png?width=90pc)
    ![CodeBuild-25](/images/3/3.1-25.png?width=90pc)

2.  Check the ECR Repository

    - Access the [Amazon Elastic Container Registry (Amazon ECR)](https://ap-northeast-2.console.aws.amazon.com/ecr)
    - Select **Repositories**.
    - Select the created Repository
      ![CodeBuild-26](/images/3/3.1-28.png?width=90pc)

    You will see the updated Images.

    ![CodeBuild-27](/images/3/3.1-26.png?width=90pc)

3.  Check the S3 Bucket

    - Access the [Amazon Simple Storage Service (Amazon S3)](https://ap-northeast-2.console.aws.amazon.com/s3)
    - Select the created Bucket
      ![CodeBuild-29](/images/3/3.1-29.png?width=90pc)

    You will see the updated Objects.

    ![CodeBuild-30](/images/3/3.1-27.png?width=90pc)

{{% notice info %}}
**Troubleshooting**
{{% /notice %}}
If you encounter an error during the build process, check the Buildspec file by:

- Select **Edit** project
- Scroll down and edit the **Buildspec** commands
