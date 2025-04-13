---
title: "Prerequisites"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: "<b> 2. </b>"
---

Before deploying applications on Amazon ECS and AWS Fargate, we need to build a basic infrastructure environment, including access rights setup, network configuration, security, and load balancing. The goal is to ensure the environment meets the conditions to run containers safely, stably, and scalably.

![ECS Networking Architecture](/images/2-prerequisites/ECS-Lab-Networking.png)

---

#### 1. IAM Roles – Service Access Permissions

**IAM Role** is a mechanism to grant temporary permissions for AWS services to access resources without using fixed credentials.

In this workshop, you will create:

- **ECS Task Execution Role**: Allows ECS to pull container images from Amazon ECR and write logs to CloudWatch.
- **ECS Service Role**: Required when ECS integrates with services like Application Load Balancer for automatic task registration/deregistration.

{{% notice note %}}
Security Best Practices: Provide sufficient Policies, ensuring ECS components have adequate permissions to operate correctly and securely.
{{% /notice %}}

---

#### 2. Networking – Virtual Network Configuration

You will configure a separate network for container applications with components:

- **VPC (Virtual Private Cloud)**: Customized virtual network, providing isolated IP address space.
- **Subnets**: Divide network into sub-zones like public subnet (Internet access) and private subnet (internal only).
- **Route Table**: Routes traffic between subnets and to the Internet.
- **Internet Gateway**: Gateway connecting public subnet to the Internet.
- **Availability Zones (AZs)**: Deploy services across multiple AZs to ensure high fault tolerance.
- **NAT Gateway**: Allows instances inside private subnet to access the Internet.

{{% notice note %}}
Provide a highly available and secure network environment to deploy containers in distributed architecture.
{{% /notice %}}

---

#### 3. Security Group – Resource-level Firewall

**Security Group** acts as a firewall, controlling inbound/outbound traffic of resources within VPC.

- Configure to allow necessary ports (HTTP - 80, HTTPS - 443, or internal ports).
- Specify allowed source IPs or services for access.

{{% notice note %}}
Ensure container applications are protected from unauthorized access in production environment.
{{% /notice %}}

---

#### 4. Load Balancer

- **Application Load Balancer (ALB)** helps distribute HTTP/HTTPS traffic to ECS Tasks.
- Create **Target Group** to manage ECS Tasks as backend targets.
- Set up **Listener Rules** to route traffic to correct target groups based on path or host.

{{% notice note %}}
Ensure scalability when distributing incoming traffic evenly between UI Services.
{{% /notice %}}