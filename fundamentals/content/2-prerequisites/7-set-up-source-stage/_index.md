---
title: "Setting up the source code stage"
date: "`r Sys.Date()`"
weight: 7
chapter: false
pre: "<b> 2.7. </b>"
---

This section covers preparing your source code repository, building Docker images, and pushing them to Amazon Elastic Container Registry (ECR) to support [automation](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/automation/vi/) tasks.

#### Connecting to the EC2 Instance

1. Select your launched EC2 instance and click **Connect**

![EC2 Connect](/images/2-prerequisites/6-set-up-lab-environment/image-2.png)
*Figure 1: EC2 Dashboard*

2. From the SSH Client tab, copy the SSH connection string:
```bash
ssh -i "ecs-workshop-key-pairs.pem" ec2-user@ec2-54-253-11-210.ap-southeast-2.compute.amazonaws.com
```

![SSH Connection Details](/images/2-prerequisites/6-set-up-lab-environment/image-3.png)
*Figure 2: SSH Connection Details*

3. Connect via SSH using your terminal

{{% notice info %}}
Ensure you provide the correct path to your private key (.pem) file on your local system.
{{% /notice %}}

![SSH Connection Success](/images/2-prerequisites/6-set-up-lab-environment/image-4.png)
*Figure 3: Successful SSH Connection*

#### Installing Docker on EC2

1. Install Docker:
```bash
sudo yum install -y docker
```

2. Start the Docker service:
```bash
sudo service docker start
```

3. Add the EC2 user to the docker group:
```bash
sudo usermod -a -G docker ec2-user
```

4. Log out and reconnect to apply the new permissions:
```bash
exit
```

5. Verify Docker installation:
```bash
docker ps
```

Expected output:
```
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS 
```

![Docker Installation Verification](/images/2-prerequisites/7-set-up-source-stage/image.png)
*Figure 4: Docker Installation Verification*

#### Forking the Repository

{{% notice note %}} 
Purpose: Create an independent copy of the source code in your personal repository to make changes without affecting the original source.
{{% /notice %}}

1. Navigate to https://github.com/aws-containers/retail-store-sample-app/fork
2. Click "Create fork" to create your personal copy

![Fork Repository](/images/2-prerequisites/7-set-up-source-stage/image-9.png)
*Figure 5: Repository Fork Interface*

#### Setting Up Git and Cloning the Repository

1. Install Git:
```bash
sudo yum install git -y
```

2. Clone and checkout specific version:
```bash
git clone https://github.com/AWS-FCJ-ECS-Workshop/retail-store-sample-app.git
git checkout tags/0.8.5
```

![Clone Repository Success](/images/2-prerequisites/7-set-up-source-stage/image-1.png)
*Figure 6: Successful Repository Clone*

#### Building Docker Image for UI Service

Navigate to the UI service directory and build the image:
```bash
cd ui
docker build -t aws-container/retail-store-sample-ui:0.8.5 .
```

![UI Service Build Success](/images/2-prerequisites/7-set-up-source-stage/image-2.png)
*Figure 7: Successful UI Service Build*

Test the image:
```bash
docker run --rm -p 8080:8080 aws-container/retail-store-sample-ui:0.8.5
```

{{% notice info %}}
Configure inbound rules for port 8080 in the `ecs-lab-public-sg` security group to allow internet access.
{{% /notice %}}

![UI Application](/images/2-prerequisites/7-set-up-source-stage/image-7.png)

#### Pushing to Amazon ECR

1. Set environment variables:
```bash
export AWS_REGION=ap-southeast-2
export AWS_ACCOUNT_ID=11111111111  # Replace with your account ID
```

2. Create ECR repository:
```bash
aws ecr create-repository \
    --repository-name aws-containers/retail-store-sample-ui \
    --region $AWS_REGION
```

![ECR Repository Creation](/images/2-prerequisites/7-set-up-source-stage/image-3.png)
*Figure 8: ECR Interface*

3. Authenticate with ECR:
```bash
aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com
```

4. Tag and push image:
```bash
docker tag aws-container/retail-store-sample-ui:0.8.5 $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/aws-containers/retail-store-sample-ui:0.8.5
docker push $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/aws-containers/retail-store-sample-ui:0.8.5
```

![Image Push Success](/images/2-prerequisites/7-set-up-source-stage/image-5.png)
*Figure 9: CLI Interface for ECR Image Push*

![ECR Console View](/images/2-prerequisites/7-set-up-source-stage/image-6.png)
*Figure 10: ECR Console Interface*

✔ In this section, we successfully forked the repository and built and pushed the image to your personal ECR repository in preparation for the [automation] chapter in this series.