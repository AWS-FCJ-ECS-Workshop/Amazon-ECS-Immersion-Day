---
title: "Create UI Target Group"
date: "`r Sys.Date()`"
weight: 7
chapter: false
pre: "<b> 7. </b>"
---

In this section, we will create a Target Group for `ui-application-tls` to support HTTPS protocol.

#### Create Target Group
1. Navigate to [EC2 Target Groups](https://console.aws.amazon.com/ec2/home#TargetGroups:)
2. Click **Create Target Group**

Configure the following settings in Specify group details:
- Select `IP Addresses` as target type

![alt text](/images/1-prerequisites/7-create-target-group/image.png)
*Figure 1. Specify Group Details*

Basic configuration:
- Target group name: `ui-application-tls`
- Protocol: `HTTPS 8080`
- VPC: `ecs-lab-vpc`

![alt text](/images/1-prerequisites/7-create-target-group/image-1.png)
*Figure 2. Target Group Configuration*

Health check configuration:
- Protocol: HTTP
- Health check path: `actuator/health`
- Health check port: 8080 (select from dropdown)

![alt text](/images/1-prerequisites/7-create-target-group/image-2.png)
*Figure 3. Health Check Configuration*

Register targets:
- VPC: `ecs-lab-vpc`
- Port: 8080

![alt text](/images/1-prerequisites/7-create-target-group/image-3.png)
*Figure 4. Register Target*

Review and create the target group

![alt text](/images/1-prerequisites/7-create-target-group/image-4.png)
*Figure 5. Target Group Creation Complete*

Export the Target Group ARN as an environment variable:

```bash
export UI_TARGET_GROUP_TLS_ARN=arn:aws:elasticloadbalancing:ap-southeast-2:XXXXXXXXXXX:targetgroup/ui-application-tls/c25180349d64205c
```

✅ You have successfully created a Target Group for UI TLS