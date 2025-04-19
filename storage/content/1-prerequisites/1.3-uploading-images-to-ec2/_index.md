---
title: "Uploading images to the Public EC2"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: "<b> 3. </b>"
---

1. Connect to the Public EC2 Instance
- If you haven't created an EC2 instance yet, please follow the instructions in [Setup lab environment](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/fundamentals/2-prerequisites/6-set-up-lab-environment/)

Navigate to EC2 Dashboard > Select your Public EC2 instance > Click Connect
![alt text](/images/1-prerequisites/1.3-uploading-images-to-ec2/image.png)
*Figure 1. EC2 Dashboard*

Copy the SSH command example and modify the SSH key path to connect to your EC2 instance
![alt text](/images/1-prerequisites/1.3-uploading-images-to-ec2/image-1.png)
*Figure 2. EC2 SSH connection details*

Verify successful SSH connection
![alt text](/images/1-prerequisites/1.3-uploading-images-to-ec2/image-2.png)
*Figure 3. Successful SSH connection to EC2*

2. Mount EFS Volume to EC2 Instance
    a. Create and configure mount point
    
    ```bash
    mkdir efs-mount-point/    # Create directory for EFS mount
    sudo yum install -y amazon-efs-utils
    sudo mount -t efs fs-078993d4880a9e44d efs-mount-point/    # Mount EFS volume
    ```
    
    ![alt text](/images/1-prerequisites/1.3-uploading-images-to-ec2/image-3.png)
    *Figure 4. Creating mount directory*
    
    b. Transfer local images to EC2 `efs-mount-point`
    
    Ensure you have an `images` folder containing the required images. You can either use your own images or [download the sample images](https://drive.google.com/drive/folders/1Zv327_rLX_Skp_NWZ0FqkA886-WEmdrn?usp=drive_link). Image names must match exactly as shown below for compatibility with the Assets application.

    ![alt text](/images/1-prerequisites/1.3-uploading-images-to-ec2/image-4.png)
    *Figure 5. Contents of local images folder*
    

    Use SCP (Secure Copy Protocol) to transfer files from your local machine to the remote EC2 instance:

    ```bash
    # Format: scp -i /path/to/ssh-key -r /path/to/local/directory username@remote-host:/destination/path
    sudo scp -i ~/.ssh/ecs-workshop-key-pairs.pem -r ~/Desktop/images/ ec2-user@ec2-54-79-164-169.ap-southeast-2.compute.amazonaws.com:.
    ```
    
    ![alt text](/images/1-prerequisites/1.3-uploading-images-to-ec2/image-5.png)
    *Figure 6. Successful file transfer to EC2*

    We successfully uploaded the images to the EC2 instance. In the following steps, we will copy these images to the mounted directory.