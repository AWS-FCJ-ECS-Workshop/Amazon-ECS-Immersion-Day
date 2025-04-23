+++
title = "Tạo IAM Roles"
date = 2020-05-14T00:38:32+07:00
weight = 2
chapter = false
pre = "<b>2.1 </b>"
+++

### 1. Tạo IAM Roles cho Code Build

1. Truy cập [IAM Dashboard](https://console.aws.amazon.com/iam/home)
2. Chọn **Access Management > Roles > Create Role**
   ![IAM-1](/images/2/2.1-1.png?width=90pc)

3. Chọn **AWS Service**

   - Service or use case: CodeBuild

   - Use case: CodeBuild, Chọn **Next**

   ![IAM-2](/images/2/2.1-2.png?width=90pc)
   ![IAM-3](/images/2/2.1-3.png?width=90pc)

   Nhập và chọn các tên policy sau:

   - [AmazonEC2ContainerRegistryFullAccess](https://us-east-1.console.aws.amazon.com/iam/home?region=ap-southeast-1#/policies/details/arn%3Aaws%3Aiam%3A%3Aaws%3Apolicy%2FAmazonEC2ContainerRegistryFullAccess)
   - [AmazonS3FullAccess](https://us-east-1.console.aws.amazon.com/iam/home?region=ap-southeast-1#/policies/details/arn%3Aaws%3Aiam%3A%3Aaws%3Apolicy%2FAmazonS3FullAccess)
   - [CloudWatchLogsFullAccess](https://us-east-1.console.aws.amazon.com/iam/home?region=ap-southeast-1#/policies/details/arn%3Aaws%3Aiam%3A%3Aaws%3Apolicy%2FCloudWatchLogsFullAccess)

4. Nhập **Role name**: `ecs-workshop-codebuild-rolling-deployment`
   ![IAM-4](/images/2/2.1-4.png?width=90pc)

5. Chọn **Add permissions**
6. Sau khi role được tạo, chọn **Add permissions**, chọn **Create inline policy**
   ![IAM-6](/images/2/2.1-6.png?width=90pc)

7. Chọn tab **JSON**

![IAM-10](/images/2/2.1-10.png?width=90pc)

Thêm đoạn JSON sau:

```
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Effect": "Allow",
			"Action": [
				"ssm:PutParameter"
			],
			"Resource": "arn:aws:ssm:$AWS_REGION:${ACCOUNT_ID}:parameter/codebuild/retail-store-sample-ui-latest-image" //Cập nhật Region, Account ID theo môi trường của bạn.
		}
	]
}
```

11. Nhập **Policy name**: `IAM-role-used-by-CodeBuild-to-allow-it-to-write-to-Parameter-Store`

12. Chọn **Create Policy**
