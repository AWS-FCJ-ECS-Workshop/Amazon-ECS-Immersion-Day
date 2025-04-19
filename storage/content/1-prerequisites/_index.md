---
title: "Prerequisites"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: "<b> 1. </b>"
---

Before starting the Storage section labs, you need to complete several prerequisite tasks to ensure proper functionality:

1. Create a security group for Amazon EFS to enable secure access to the file system
2. Configure the ECS IAM Role with appropriate permissions for EFS management
3. Set up file transfer from your local environment to an EC2 instance with mounted EFS storage

These prerequisites are essential for establishing secure communication between services and enabling proper file system access.

#### Prerequisite Steps

Complete these tasks in sequence:

1. [Create Security Group](1.1-create-security-group/)
2. [Create EFS File System](1.2-create-efs/)
3. [Upload Images to Public EC2 Instance](1.3-uploading-images-to-ec2/)

Each step provides detailed instructions to configure the required components for the workshop environment. Ensure you complete all prerequisites before proceeding to the main lab exercises.