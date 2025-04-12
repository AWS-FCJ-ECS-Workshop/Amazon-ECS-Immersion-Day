---
title: "Networking - Virtual Network Configuration"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: "<b> 2.2. </b>"
---

### Introduction

[Amazon Virtual Private Cloud (Amazon VPC)](https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html) is a service that lets you create a completely isolated virtual network environment in the AWS Cloud. In this environment, you can deploy and manage AWS resources flexibly according to your organization's needs.

![Networking](/images/2-prerequisites/2-networking/ECS-Lab-Networking.png)

In this section, we will learn how to set up the following basic network components:

1. Virtual Private Cloud (VPC)
2. Subnets (Public and Private)
3. Internet Gateway
4. Network Address Translation (NAT) Gateway
5. Route Tables

### Creating VPC

#### Step 1: Access VPC Dashboard

1. Log in to AWS Management Console
2. Access [VPC Dashboard](http://console.aws.amazon.com/vpc/home)
3. Select **Create VPC**

![alt text](/images/2-prerequisites/2-networking/image.png)

#### Step 2: Configure VPC

In the Create VPC interface:

1. Select **VPC and More**
2. Configure the parameters:
   - Name tag: Set a name for VPC
   - IPv4 CIDR block: `10.0.0.0/16`
   - Availability Zones: Choose 2 AZs to ensure high availability
   - Subnets: 2 Public Subnets and 2 Private Subnets
   - NAT Gateway: Create in 1 AZ
   - VPC endpoints: Don't create S3 Gateway

![alt text](/images/2-prerequisites/2-networking/image-1.png)

{{% notice note %}}
Use the Preview Map to confirm the network structure meets your requirements.
{{% /notice %}}

#### Step 3: Confirm and Create VPC

AWS will automatically create network components sequentially according to the selected configuration.

![alt text](/images/2-prerequisites/2-networking/image-2.png)

After completion, you can view VPC configuration details:

![alt text](/images/2-prerequisites/2-networking/image-3.png)

### Summary

In this section, you have completed setting up basic network infrastructure on AWS Cloud. This is an important and necessary step before deploying any AWS services. This network structure will ensure security and efficiency for your applications.