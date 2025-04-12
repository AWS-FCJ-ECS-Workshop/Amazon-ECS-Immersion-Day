---
title: "Security Group - Resource-level Firewall"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: "<b> 2.3. </b>"
---

### Introduction

[Security Group (SG)](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-security-groups.html#:~:text=A%20security%20group%20controls%20the,with%20a%20default%20security%20group.) is a virtual firewall that protects AWS resources from unauthorized access. Security Group controls inbound and outbound network traffic for resources by defining rules.

![ECS-lab-security-group](/images/2-prerequisites/3-security-groups/ECS-Lab-Security Group.png)

### Main Components of Security Group

#### 1. Inbound Rules
- Controls traffic allowed to access Network Interface (ENI)
- Includes components:
  - Source: Source IP address or Security Group ID
  - Protocol: Allowed network protocol (TCP, UDP, ICMP,...)
  - Port Range: Allowed access port range

{{% notice info %}}
By default, Security Group uses whitelisting mechanism - denying all traffic access until explicitly permitted through inbound rules.
{{% /notice %}}

#### 2. Outbound Rules
- Controls outgoing traffic from resources
- Includes:
  - Destination: Destination IP address or Security Group ID
  - Protocol: Allowed network protocol
  - Port Range: Allowed destination port range

{{% notice info %}}
By default, Security Group allows all outbound traffic. If this rule is deleted, the resource cannot connect to the Internet.
{{% /notice %}}

#### 3. Stateful Feature
Security Group operates on a stateful mechanism - when a request is allowed in, its response is automatically allowed out without needing to define in outbound rules.

### Practice: Creating Security Groups

We will create 3 Security Groups for our architecture:

#### 1. Public Security Group
**Purpose**: Protect Application Load Balancer (ALB)

**Steps**:
1. Access [Security Group Dashboard](console.aws.amazon.com/ec2/home#SecurityGroups)
2. Select Create Security Group
3. Configure:
   - Name: `ecs-lab-public-vpc`
   - VPC: `ecs-lab-vpc`
   - Inbound Rules:
     - Port 80 (HTTP)
     - Port 443 (HTTPS)
   - Outbound Rules: Allow all traffic
4. Add tags (optional)
5. Select Create Security Group

![Public SG Creation](/images/2-prerequisites/3-security-groups/image.png)
![Public SG Success](/images/2-prerequisites/3-security-groups/image-1.png)

#### 2. Private Security Group
**Purpose**: Protect Amazon Fargate tasks in Private Subnet

**Steps**:
1. Create new Security Group
2. Configure:
   - Name: `ecs-lab-private-sg`
   - VPC: `ecs-lab-vpc`
   - Inbound Rules:
     - Port 8080 (TCP) from Public Security Group
   - Outbound Rules: Allow all traffic

{{% notice note %}}
Security Best Practices: Only allow Inbound Rule TCP 8080 from Public SG to ensure only ALB can access instances in Private Subnet.
{{% /notice %}}

![Private SG Creation](/images/2-prerequisites/3-security-groups/image-2.png)
![Private SG Success](/images/2-prerequisites/3-security-groups/image-3.png)

#### 3. UI Security Group
**Purpose**: Protect Fargate tasks for UI Service

**Configuration**:
- Name: `ecs-lab-ui-sg`
- VPC: `ecs-lab-vpc`
- Inbound Rules:
  - Port 8080 (TCP) from Public Security Group
- Outbound Rules: Allow all traffic

![UI Security Group](/images/2-prerequisites/3-security-groups/image-4.png)

![Created UI SG](/images/2-prerequisites/3-security-groups/image-5.png)