+++
title = "Create IAM Roles"
date = 2020-05-14T00:38:32+07:00
weight = 2
chapter = false
pre = "<b>2.1 </b>"
+++

### 1. Create IAM Roles for Code Build

1. Access [IAM Dashboard](https://console.aws.amazon.com/iam/home)
2. Select **Access Management > Roles > Create Role**
   ![IAM-1](/images/2/2.1-1.png?width=90pc)

3. Select **AWS Service**

   - Service or use case: CodeBuild

   - Use case: CodeBuild, Select **Next**

   ![IAM-2](/images/2/2.1-2.png?width=90pc)
   ![IAM-3](/images/2/2.1-3.png?width=90pc)

   Enter and select the following policy names:

   - [AmazonEC2ContainerRegistryFullAccess](https://us-east-1.console.aws.amazon.com/iam/home?region=ap-southeast-1#/policies/details/arn%3Aaws%3Aiam%3A%3Aaws%3Apolicy%2FAmazonEC2ContainerRegistryFullAccess)
   - [AmazonS3FullAccess](https://us-east-1.console.aws.amazon.com/iam/home?region=ap-southeast-1#/policies/details/arn%3Aaws%3Aiam%3A%3Aaws%3Apolicy%2FAmazonS3FullAccess)
   - [CloudWatchLogsFullAccess](https://us-east-1.console.aws.amazon.com/iam/home?region=ap-southeast-1#/policies/details/arn%3Aaws%3Aiam%3A%3Aaws%3Apolicy%2FCloudWatchLogsFullAccess)

4. Enter **Role name**: `ecs-workshop-codebuild-rolling-deployment`
   ![IAM-4](/images/2/2.1-4.png?width=90pc)

5. Select **Add permissions**
6. After the role is created, select **Add permissions**, select **Create inline policy**
   ![IAM-6](/images/2/2.1-6.png?width=90pc)

7. Select tab **JSON**

![IAM-10](/images/2/2.1-10.png?width=90pc)

Add the following JSON:

```
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Effect": "Allow",
			"Action": [
				"ssm:PutParameter"
			],
			"Resource": "arn:aws:ssm:$AWS_REGION:${ACCOUNT_ID}:parameter/codebuild/retail-store-sample-ui-latest-image" //Change your Region name, Account ID
		}
	]
}
```

11. Enter **Policy name**: `IAM-role-used-by-CodeBuild-to-allow-it-to-write-to-Parameter-Store`

12. Select **Create Policy**
