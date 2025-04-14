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

### Granting Permissions to EC2 Instance

To allow an EC2 instance to execute AWS CLI commands on your behalf, we need to assign an IAM Role to the EC2 Service. In this lab, we will create an IAM Role (to be assumed by EC2) and assign the `AdministratorAccess` Policy to this role.

{{% notice warning %}}
Using the `AdministratorAccess` Policy is only suitable for development environments. When deploying systems in Production environments, you should follow the principle of least privilege - only granting the minimum necessary permissions to Users and Services.
{{% /notice %}}

#### Implementation Steps:

1. **Create a New IAM Role**
   - Navigate to [IAM Roles Dashboard](https://console.aws.amazon.com/iam/home#/roles)
   - Click **Create Role**

![alt text](/images/2-prerequisites/6-set-up-lab-environment/image-5.png)

2. **Configure Trust Policy**
   - In the **Select trusted entity** section:
     - Choose **AWS Services**
     - Use case: **EC2**
   - Click **Next**

![alt text](/images/2-prerequisites/6-set-up-lab-environment/image-6.png)

3. **Add Permissions**
   - Search and select the **AdministratorAccess** policy
   - Click **Next**

![alt text](/images/2-prerequisites/6-set-up-lab-environment/image-7.png)

4. **Name and Review Configuration**
   - Enter Role name
   - Review all configurations
   - Click **Create Role**

![alt text](/images/2-prerequisites/6-set-up-lab-environment/image-8.png)
![alt text](/images/2-prerequisites/6-set-up-lab-environment/image-9.png)

5. **Attach Role to EC2 Instance**
   - Navigate to [EC2 Instances Dashboard](https://console.aws.amazon.com/ec2#Instances)
   - Select the target EC2 instance
   - From the **Actions** menu, select **Security** > **Modify IAM Role**

![alt text](/images/2-prerequisites/6-set-up-lab-environment/image-10.png)

   - In the **Modify IAM Role** interface:
     - Select the IAM Role `ecs-lab-admin-ec2`
     - Click **Update IAM Role**

![alt text](/images/2-prerequisites/6-set-up-lab-environment/image-11.png)

After completing these steps, your EC2 instance has been granted permissions and can execute AWS CLI commands to interact with other AWS services.


### Connecting to Your EC2 Instance

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