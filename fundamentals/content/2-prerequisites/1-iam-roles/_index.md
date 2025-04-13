---
title: "IAM Roles - Service Access Authorization"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: "<b> 2.1. </b>"
---

### Introduction

[IAM Role](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html) is a feature that enhances security on AWS. An IAM Role can be temporarily assigned to IAM Users and AWS resources both internally and externally to your account. For example, when an IAM User assumes an IAM Role, that user temporarily gains the permissions of that role. You should use IAM Roles when you want to provide short-term access to an IAM User or AWS resource.

For an IAM User to assume an IAM Role, the role itself needs to allow the user to perform the assume action (trust policy).

An important characteristic of IAM Roles is that they don't have credentials, so you cannot directly log into AWS accounts using IAM Roles.

![IAM ROLE](/images/2-prerequisites/1-iam-roles/ECS-Lab-IAM-Role.png)
---
#### 2. Create IAM Role for ECS Task Execution

This role allows ECS to perform system tasks like pulling container images and writing logs.

1. Access [IAM Dashboard](https://console.aws.amazon.com/iam/home)
2. Select **Access Management** > **Roles** > **Create Role**

![IAM Role Dashboard](/images/2-prerequisites/1-iam-roles/image.png)

3. Configure role:
   - **Trusted Entity Type**: `AWS Service`
   - **Use Case**: `Elastic Container Service`
   - **Service or Use Case**: `Elastic Container Task`
   - Click **Next**

![Select ECS Task](/images/2-prerequisites/1-iam-roles/image-1.png)

4. Add permissions:
   - Search and select `AmazonECSTaskExecutionRolePolicy`

![Select Policy](/images/2-prerequisites/1-iam-roles/image-2.png)

5. Name the role: `retailStoreECSTaskExecutionRole`

{{% notice warning %}}
The role name must be exactly `retailStoreECSTaskExecutionRole` to be compatible with CLI commands!
{{% /notice %}}

![Confirm Role](/images/2-prerequisites/1-iam-roles/image-3.png)

Result after successful creation:

![Created Role](/images/2-prerequisites/1-iam-roles/image-4.png)

#### 2. Create IAM Role for ECS Task (Application Role)

This role allows applications in containers to access AWS services and execute commands.

1. At [IAM Dashboard](https://console.aws.amazon.com/iam/home):
   - Select **Roles** > **Create Role**
   - **Trusted Entity Type**: `AWS Service`
   - **Use case**: `Elastic Container Service`
   - **Service or Use Case**: `Elastic Container Task`

![Select ECS Task](/images/2-prerequisites/1-iam-roles/image-1.png)

2. Skip Add permissions step (will add inline policy later)

3. Name the role: `retailStoreEcsTaskRole`

{{% notice warning %}}
The role name must be exactly `retailStoreEcsTaskRole` to be compatible with CLI commands!
{{% /notice %}}

![Create ECS Task Role](/images/2-prerequisites/1-iam-roles/image-5.png)

![Complete Role Creation](/images/2-prerequisites/1-iam-roles/image-7.png)

#### Add Inline Policy for ECS Task Role

To allow ECS Tasks to execute remote commands via Systems Manager and interact with ECS API:

1. Select role > **Add permissions** > **Create inline policy**

![Role Detail](/images/2-prerequisites/1-iam-roles/image-6.png)

2. Switch to **JSON** tab, add the following policy:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ssmmessages:CreateControlChannel",
        "ssmmessages:CreateDataChannel", 
        "ssmmessages:OpenControlChannel",
        "ssmmessages:OpenDataChannel"
      ],
      "Resource": "*"
    },
    {
      "Effect": "Allow", 
      "Action": [
        "ecs:ExecuteCommand",
        "ecs:DescribeTasks"
      ],
      "Resource": [
        "arn:aws:ecs:${var.aws_region}:${var.aws_account_id}:task/retail-store-ecs-cluster/*", // Replace environment variables
        "arn:aws:ecs:${var.aws_region}:${var.aws_account_id}:cluster/*" // Replace environment variables
      ]
    }
  ]
}
```

![Paste JSON Policy](/images/2-prerequisites/1-iam-roles/image-8.png)

3. Review and create policy:

![Name Policy](/images/2-prerequisites/1-iam-roles/image-9.png)

Confirm policy has been attached:

![Confirm policy](/images/2-prerequisites/1-iam-roles/image-10.png)

---
### Summary

You have completed:
- Creating IAM Role for EC2
- Creating Execution Role for ECS Task
- Creating Application Role for ECS Task with inline policy
- Setting up secure access for system components