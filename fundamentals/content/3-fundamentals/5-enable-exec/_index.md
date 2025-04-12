---
title: "Enable ECS Exec"
date: "`r Sys.Date()`"
weight: 5
chapter: false
pre: "<b> 3.5. </b>"
---

### Enable ECS Exec

This section will guide you through enabling the ECS Exec feature to run commands or access shell in containers running on EC2 instances or Fargate. ECS Exec provides many benefits for operations management and security. This feature allows access control to containers in ECS tasks, supports secure troubleshooting with logging without requiring SSH access to hosts.

Through policies and IAM Roles, you can strictly control who can execute commands in containers, thereby enhancing overall security. Additionally, all commands executed via ECS Exec are logged in CloudWatch, creating an audit trail for compliance and monitoring. Detailed information can be found [here](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-exec.html).

Follow these steps to enable and use ECS Exec:

1. Set up IAM Role for users
2. Set up IAM Role for ECS task
3. Set up environment
4. Enable Amazon ECS Exec on service
5. Connect to ECS task

---
#### Set up IAM Role for users

Since you'll be using ECS Exec from IDE, ensure the IAM Role attached to the IDE has the necessary IAM policies. Update the IAM Role associated with the EC2 instance running the IDE by adding the following inline policy:

```bash
cat << EOF > ecs-exec-command-policy.json
{
    "Version": "2012-10-17", 
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ecs:ExecuteCommand",
                "ecs:DescribeTasks"
            ],
            "Resource": [
                "arn:aws:ecs:${AWS_REGION}:${ACCOUNT_ID}:task/retail-store-ecs-cluster/*",
                "arn:aws:ecs:${AWS_REGION}:${ACCOUNT_ID}:cluster/*"
            ]
        }
    ]
}
EOF

aws iam put-role-policy \
    --role-name $(aws sts get-caller-identity --query 'Arn' | cut -d'/' -f2) \
    --policy-name AmazonECSExecCommand \
    --policy-document file://ecs-exec-command-policy.json
```

<details>
<summary>Check if the policy has been configured correctly</summary>
In this lab, you only add the most basic IAM policy. Additional IAM policies may be needed to use ECS Exec features like CloudWatch logs.
</details>

#### Set up IAM Role for ECS Task

ECS Exec requires a task IAM Role for SSM communication. We have already created this IAM Role for the UI Service task. Let's check its permissions:

```bash
aws iam get-role-policy \
    --role-name  retailStoreEcsTaskRole \
    --policy-name $(aws iam list-role-policies --role-name retailStoreEcsTaskRole --query 'PolicyNames[0]' --output text)
```

The result will be as follows:

```json
{
    "RoleName": "retailStoreEcsTaskRole",
    "PolicyName": "RetailStoreEcsTaskRoleDefaultPolicy3AF87919",
    "PolicyDocument": {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Action": [
                    "ssmmessages:CreateControlChannel",
                    "ssmmessages:CreateDataChannel", 
                    "ssmmessages:OpenControlChannel",
                    "ssmmessages:OpenDataChannel"
                ],
                "Resource": "*",
                "Effect": "Allow"
            }
        ]
    }
}
```

#### Set up environment

You need the AWS CLI and Session Manager plugin for AWS CLI. Both utilities are pre-installed in the IDE for this workshop. Please refer to the linked documentation for setup in your own environment.

#### Enable Amazon ECS Exec on task

Update the UI Service to enable Amazon ECS Exec using the `--enable-execute-command` flag:

```bash
aws ecs update-service \
    --cluster retail-store-ecs-cluster \
    --service ui \
    --task-definition retail-store-ecs-ui \
    --enable-execute-command \
    --force-new-deployment
```

Wait for ECS to deploy the changes to the service (about 5 minutes):

```bash
aws ecs wait services-stable --cluster retail-store-ecs-cluster --services ui
```

Run the following command to select one of the running UI tasks with `enableExecuteCommand` enabled:

```bash
ECS_EXEC_TASK_ARN=$(aws ecs list-tasks --cluster retail-store-ecs-cluster \
    --service-name ui --query 'taskArns[]' --output text | \
    xargs -n1 aws ecs describe-tasks --cluster retail-store-ecs-cluster --tasks | \
    jq -r '.tasks[] | select(.enableExecuteCommand == true) | .taskArn' | \
    head -n 1)
```

Check the result with echo command:

```bash
echo $ECS_EXEC_TASK_ARN
# arn:aws:ecs:ap-southeast-2:XXXXXXXXXXX:task/retail-store-ecs-cluster/0a2039141c054846b4dfc7983e49f64a
```

---
### Connect to ECS Task

Start an interactive `/bin/bash` session in the running task:

```bash
if [ -z ${ECS_EXEC_TASK_ARN} ]; then echo "ECS_EXEC_TASK_ARN is not correctly configured!"; else
aws ecs execute-command --cluster retail-store-ecs-cluster \
    --task $ECS_EXEC_TASK_ARN \
    --container application \
    --interactive \
    --command "/bin/bash"
fi
```

You will see output like this:

```
The Session Manager plugin was installed successfully. Use the AWS CLI to start a session.

Starting session with SessionId: ecs-execute-command-vvdysulqbcz2txr2d262sw2s64
bash-5.2#
```

In the new session, you can execute these commands:

```bash
cat /etc/os-release

df -h
```

![alt text](/images/3-fundamentals/5-enable-exec/image-1.png)

To end the session:

```bash
exit
```