---
title : "Amazon ECS Series: Security"
date :  "`r Sys.Date()`" 
chapter : false
---

# Security

{{% notice info %}}
Prerequisites: Complete the [Fundamentals](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/fundamentals/) and [ECS Service Connect](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/networking/) chapters before starting this lab.
{{% /notice %}}

Amazon ECS Fargate provides serverless container orchestration that eliminates infrastructure management overhead. This workshop guides you through implementing comprehensive security controls for your containerized applications on ECS Fargate, covering critical aspects such as:

- Network security configuration
- Identity and access management
- Secure secrets handling
- Compliance requirements
- Runtime security monitoring

#### Cost Estimation

| Service               | Region                  | Upfront | Monthly | 12-Month Total | Currency | Configuration Summary |
|-----------------------|-------------------------|---------|---------|----------------|----------|------------------------|
| AWS Fargate           | Asia Pacific (Sydney)   | 0       | 0.45    | 5.40           | USD      | Linux, x86, 15 minutes per task daily, 20 GB storage, 2 GB memory |
| DynamoDB On-Demand    | Asia Pacific (Sydney)   | 0       | 2.85    | 34.20          | USD      | Standard tables, 5 KB items, 10 GB storage |
| Amazon GuardDuty      | Asia Pacific (Sydney)   | -       | -       | -              | USD      | Container runtime monitoring |
| **Total**             |                         | **0**   | **3.30**| **39.60**      | USD      |                        |

#### Workshop Modules

1. [Shared Responsibility Model](1-shared-responsibility-model/)
2. [Prerequisites](2-prerequisites/)
3. [IAM Roles](3-iam-roles/)
   
    3.1. [Cart Service Creation](3-iam-roles/3.1-create-cart-service/)

    3.2. [IAM Permission Configuration](3-iam-roles/3.2-fix-iam-permissions/)

    3.3. [UI Service Updates](3-iam-roles/3.3-update-ui-service/)

    3.4. [DynamoDB Integration Testing](3-iam-roles/3.4-test-dynamodb-integration/)

4. [Amazon GuardDuty](4-amazon-guardduty/)

    4.1. [GuardDuty Configuration Review](4-amazon-guardduty/4.1-review-guardduty-setup/)

    4.2. [IAM Policy Implementation](4-amazon-guardduty/4.2-attach-iam-policy/)

    4.3. [Runtime Monitoring with GuardDuty](4-amazon-guardduty/4.3-guardduty-monitoring/)

    4.4. [Malicious Task Simulation](4-amazon-guardduty/4.4-create-malicious-task)
    
5. [Resource Cleanup](5-clean-resources/)
6. [Conclusion](6-conclusion/)