---
title: "Catalog IAM Role"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: "<b> 3. </b>"
---

In this section, we will create a Catalog Task Execution Role that enables the Catalog service to interact with AWS services on your behalf.

#### Creating the Catalog Task Execution Role

Navigate to [IAM Role Dashboard](https://console.aws.amazon.com/iam/home/roles) and select Create Role

In the Select trusted entity interface:
- Select AWS Service
- Choose Elastic Container Service use case
- Select Elastic Container Service Task

![Select trusted entity interface](/images/1-prerequisites/3-iam-role/image.png)
*Figure 1. Select trusted entity interface*

Under Add Permission, attach the `AmazonECSTaskExecutionRolePolicy` > Next

![Add Permission interface](/images/1-prerequisites/3-iam-role/image-1.png)
*Figure 2. Add Permission interface*

After successful creation, access the role details. Under the **Permissions** tab, select the dropdown menu > Create Inline Policy

![Catalog Role Details](/images/1-prerequisites/3-iam-role/image-2.png)
*Figure 3. Catalog Role Details*

Access the Policy Editor > JSON tab and add the following policy:

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

Add this policy through the Policy Editor

![Adding inline policy](/images/1-prerequisites/3-iam-role/image-3.png)
*Figure 4. Adding inline policy*

In the Review and Create step, name the policy `catalogTaskExecutionPolicy`

![Review and Create step](/images/1-prerequisites/3-iam-role/image-4.png)
*Figure 5. Review and Create step*

Successful inline policy addition:

![Successful inline policy addition](/images/1-prerequisites/3-iam-role/image-5.png)
*Figure 6. Successful inline policy addition*