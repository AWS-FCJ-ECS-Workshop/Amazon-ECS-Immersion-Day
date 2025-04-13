---
title: "Set up lab environment"
date: "`r Sys.Date()`"
weight: 6
chapter: false
pre: "<b> 2.6. </b>"
---

### Setting Up Your Lab Environment

In this section, you will configure an Amazon EC2 instance and associated network resources required for the lab environment. This setup is essential for the [Storage](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/storage) section, where we'll need to mount an Amazon EFS file system to the EC2 instance for managing asset services.

![Lab Environment Architecture](/images/2-prerequisites/6-set-up-lab-environment/ECS-Lab-Networking-EC2-environment.png)

### Launching an EC2 Instance

1. Navigate to the EC2 Dashboard and select **Launch Instance**

2. Configure the following instance details:
   - Amazon Machine Image (AMI): Amazon Linux 2023
   - Instance Type: t2.micro
   - Key Pair: Create new or select existing

![Instance Configuration](/images/2-prerequisites/6-set-up-lab-environment/image.png)

3. Configure Network Settings:
   - Select **Edit**
   - VPC: `ecs-lab-vpc`
   - Subnet: `ecs-lab-subnet-public1`
   - Auto-assign Public IP: Enable
   - Security Group: Select existing `ecs-lab-public-sg` (configured for HTTPS, HTTP, and SSH access)

![Network Configuration](/images/2-prerequisites/6-set-up-lab-environment/image-1.png)

4. Select **Launch Instance**

## Connecting to Your EC2 Instance

1. Select your launched EC2 instance and click **Connect**

![EC2 Connect](/images/2-prerequisites/6-set-up-lab-environment/image-2.png)

2. In the SSH Client tab, copy the SSH connection string:
```bash
ssh -i "ecs-workshop-key-pairs.pem" ec2-user@ec2-54-253-11-210.ap-southeast-2.compute.amazonaws.com
```

![SSH Connection Details](/images/2-prerequisites/6-set-up-lab-environment/image-3.png)

3. Open your terminal, paste the SSH command, and execute

{{% notice info %}}
**Important**: Ensure you specify the correct path to your private key file on your local system.
{{% /notice %}}

![SSH Connection Success](/images/2-prerequisites/6-set-up-lab-environment/image-4.png)

### Summary

You have now successfully:
- Launched an EC2 instance in your lab VPC
- Configured appropriate network settings
- Established SSH connectivity
- Prepared the environment for mounting EFS and managing asset services

This completes the lab environment setup required for subsequent sections of the workshop.