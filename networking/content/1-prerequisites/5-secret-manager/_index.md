---
title: "Creating Parameters in AWS Secrets Manager"
date: "`r Sys.Date()`"
weight: 5
chapter: false
pre: "<b> 5. </b>"
---

#### AWS Systems Manager
AWS Systems Manager enables you to manage AWS resources at scale across AWS, hybrid, and multi-cloud environments.

#### AWS Secrets Manager
AWS Secrets Manager helps you protect access to your applications, services, and IT resources. This service enables you to easily rotate, manage, and retrieve database credentials, API keys, and other secrets throughout their lifecycle.

#### Create Database Endpoint Parameter

After successfully creating your database instance, access the instance details and copy the endpoint:

![alt text](/images/1-prerequisites/5-secret-manager/image.png)
*Figure 1: Database instance details created in section 4*

```bash
# Replace --value with your endpoint
aws ssm put-parameter \
  --name "/retail-store-ecs/catalog/db-endpoint" \
  --value "<your-mysql-url>:3306" \
  --type "String" \
  --overwrite
  
aws ssm get-parameter --name "/retail-store-ecs/catalog/db-endpoint"
```

#### Create Secret Values

{{% notice info %}}
Note: Replace `username` & `password` if you used different values when creating the database.
{{% /notice %}}

```bash
aws secretsmanager create-secret \
  --name "retail-store-ecs-catalog-db" \
  --secret-string '{"username":"admin","password":"admin123"}'
  
aws secretsmanager get-secret-value --secret-id "retail-store-ecs-catalog-db"
```

![alt text](/images/1-prerequisites/5-secret-manager/image-1.png)
*Figure 2: Successfully created values in AWS Secrets Manager*