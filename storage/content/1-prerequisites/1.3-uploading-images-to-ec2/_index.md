---
title: "Uploading images to the Public EC2"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: "<b> 3. </b>"
---

1. Connect to the public EC2
- If you didn’t create yet! please create on - refer to [Setup lab environment](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/fundamentals/2-prerequisites/6-set-up-lab-environment/)


Head to EC2 Dashboard > Select created Public EC2 > Select Connect
![alt text](/images/1-prerequisites/1.3-uploading-images-to-ec2/image.png)
*Figure 1. EC2 Dashboard*

Copy SSH Example, change SSH path, then ssh into EC2
![alt text](/images/1-prerequisites/1.3-uploading-images-to-ec2/image-1.png)
*Figure 2. EC2 SSH interface*

SSH successfully
![alt text](/images/1-prerequisites/1.3-uploading-images-to-ec2/image-2.png)
*Figure 2. SSH to EC2 successfully*

1. Mounting EFS volume to EC2
    a. Making mounting point
    
    ```bash
    mkdir efs-mount-point/ # creating folder to mount efs volume
    sudo yum install -y amazon-efs-utils
    sudo mount -t efs fs-078993d4880a9e44d efs-mount-point/ # mount efs into mounting point
    ```
    
    ![alt text](/images/1-prerequisites/1.3-uploading-images-to-ec2/image-3.png)
    *Figure 3. Making mounting directory*
    
    b.  Copy your local images → EC2 `efs-mount-point`
    
    Inside local file - note that there is `images` folder, you can find images by yourself or [download images here](), but it must match exactly name in below image, cuz its config in Assets application.


    ![alt text](/images/1-prerequisites/1.3-uploading-images-to-ec2/image-4.png)
    *Figure 4. List images inside local folder `images`*
    

    SCP (securely copy file) used to moving local file to remote server over network.

    ```bash
    # scp -i /path/to/ssh-key -r /path/to/local/directory username@192.168.1.2:/home/username/
    sudo scp -i ~/.ssh/ecs-workshop-key-pairs.pem -r ~/Desktop/images/ ec2-user@ec2-54-79-164-169.ap-southeast-2.compute.amazonaws.com:.
    ```
    
    ![alt text](/images/1-prerequisites/1.3-uploading-images-to-ec2/image-5.png)
    *Figure 5. Moving local file to EC2 successfully*
