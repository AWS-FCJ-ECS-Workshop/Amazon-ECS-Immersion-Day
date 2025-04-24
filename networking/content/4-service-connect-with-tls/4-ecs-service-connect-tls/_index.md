---
title: "ECS Service Connect TLS Verification"
date: "`r Sys.Date()`"
weight: 4
chapter: false
pre: "<b> 4.4 </b>"
---

After completing the ECS Service Connect setup, verify that TLS is properly enabled. This section guides you through the verification process using the following steps:

1. Retrieve task private IP addresses
2. Connect to a task with execute command capability
3. Install OpenSSL for certificate verification
4. Verify the TLS certificate configuration

#### Retrieve Task Private IP Addresses

{{% notice note %}}
Direct DNS name queries will not expose the certificate information.
{{% /notice %}}

Execute these commands to obtain and store the private IP address of a running **UI-TLS** task:

```bash
task_arn=$(aws ecs list-tasks --cluster retail-store-ecs-cluster \
  --service-name ui-tls \
  --query 'taskArns[0]' --output text)

UI_TLS_PRIVATE_IP=$(aws ecs describe-tasks --cluster retail-store-ecs-cluster --tasks $task_arn \
  | jq -r '.tasks[].containers[] | select(.name == "application") | .networkInterfaces[0].privateIpv4Address')
```

Confirm the IP address:

```bash
echo ${UI_TLS_PRIVATE_IP}
```

#### Connect to Task with Execute Command

Select a **UI** task that has execute command capability:

```bash
export ECS_EXEC_TASK_ARN=$(aws ecs list-tasks --cluster retail-store-ecs-cluster \
  --service-name ui --query 'taskArns[0]' --output text | \
  xargs -n1 aws ecs describe-tasks --cluster retail-store-ecs-cluster --tasks | \
  jq -r '.tasks[] | select(.enableExecuteCommand == true) | .taskArn' | \
  head -n 1)

echo ${ECS_EXEC_TASK_ARN}
```

Transfer the IP address variable to the task:

```bash
aws ecs execute-command --cluster retail-store-ecs-cluster \
  --task $ECS_EXEC_TASK_ARN \
  --container application \
  --interactive \
  --command "/bin/bash -c 'echo UI_TLS_PRIVATE_IP=$(echo $UI_TLS_PRIVATE_IP) > /app/private_ip.env'"
```

Initiate an interactive shell session:

```bash
aws ecs execute-command --cluster retail-store-ecs-cluster \
  --task $ECS_EXEC_TASK_ARN \
  --container application \
  --interactive \
  --command "/bin/bash"
```

#### Configure the Environment

Import the environment variable:

```bash
source /app/private_ip.env
```

Install OpenSSL:

```bash
yum install openssl -y
```

#### Verify TLS Certificate

Examine the certificate details:

```bash
openssl s_client -connect $UI_TLS_PRIVATE_IP:8080 < /dev/null 2> /dev/null \
  | openssl x509 -noout -text
```

The output should display certificate details including:
- Version and serial number
- Signature algorithm
- Issuer information
- Validity period
- Subject details
- Public key information
- X.509v3 extensions
- Signature

To exit the session:

```bash
exit
```