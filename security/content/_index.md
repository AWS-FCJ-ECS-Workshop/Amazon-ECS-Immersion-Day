---
title : "Amazon ECS Series: Security"
date :  "`r Sys.Date()`" 
chapter : false
---

# Security

{{% notice info %}}
// warning: finish Fundamentals and ECS service connect!
{{% /notice %}}


Amazon ECS Fargate offers a serverless approach to running containerized applications, enabling developers to concentrate on their application logic without the need to manage the underlying infrastructure. However, ensuring robust security for these applications is paramount. This guide delves into the essential aspects of security in ECS Fargate, encompassing network isolation, IAM roles, secrets management, and compliance considerations.

#### Detailed Estimate

| Service               | Region                  | Upfront | Monthly | 12-Month Total | Currency | Configuration Summary |
|-----------------------|-------------------------|---------|---------|----------------|----------|------------------------|
| AWS Fargate           | Asia Pacific (Sydney)   | 0       | 0.45    | 5.40           | USD      | Linux, x86, 15 min/task/day, 20 GB storage, 2 GB memory |
| DynamoDB On-Demand    | Asia Pacific (Sydney)   | 0       | 2.85    | 34.20          | USD      | Standard, 5 KB item, 10 GB storage |
| Amazon GuardDuty      | Asia Pacific (Sydney)   |        |        |            | USD      |                        |
| **Total**             |                         | **0**   | **3.30**| **39.60**      | USD      |                        |

#### Key Concepts

1. [Shared Responbility Model](1-shared-responsibility-model/)
2. [IAM roles](2-iam-roles/)
   
    2.1. [Create Cart service](2.1-create-cart-service/)

    2.2. [Fix IAM permissions](2.2-fix-iam-permissions/)

    2.3. [Update the UI Service](2.3-update-ui-service/)

    2.4. [Test the DynamoDB integration](2.4-test-dynamo-db/)

3. [Amazon GuardDuty](3-amazon-guardduty/)

    3.1. [Review GuardDuty setup](3.1-review-guardduty-setup/)

    3.2. [Attach necessary IAM Policy](3.2-attach-iam-policy/)

    3.3. [GuardDuty runtime monitoring](3.3-guardduty-runtime-monitoring)

    3.4. [Create a Malicious Task](3.4-create-malicious-task)
    
4. [Clean Resources](4-clean-resources/)
5. [Conclusion](5-conclusion/)