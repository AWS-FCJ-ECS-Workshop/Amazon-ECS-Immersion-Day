---
title: "ECS Service Connect TLS Verification"
date: "`r Sys.Date()`"
weight: 4
chapter: false
pre: "<b> 4.4 </b>"
---

Now that we have completed the ECS Service Connect setup, we will verify our configuration to ensure that ECS Service Connect TLS is correctly enabled. In this section, we will perform the following steps:

- Retrieve the private IP of tasks  
- Connect to a running task with `enableExecuteCommand` enabled  
- Install the `openssl` command on the running container  
- Verify the TLS certificate  

---

### Retrieve the private IP of tasks

{{% notice note %}}
Calling the DNS name directly does not reveal the certificate.
{{% /notice %}}

Run the following command to retrieve the private IP address of a running **UI-TLS** task and save the result in an environment variable:

```bash
task_arn=$(aws ecs list-tasks --cluster retail-store-ecs-cluster \
  --service-name ui-tls \
  --query 'taskArns[0]' --output text)

UI_TLS_PRIVATE_IP=$(aws ecs describe-tasks --cluster retail-store-ecs-cluster --tasks $task_arn \
  | jq -r '.tasks[].containers[] | select(.name == "application") | .networkInterfaces[0].privateIpv4Address')
```

You can verify the IP address with:

```bash
echo ${UI_TLS_PRIVATE_IP}
```

---

### Connect to a running task with `enableExecuteCommand` enabled

Run the following command to select one of the **UI** tasks with `enableExecuteCommand` enabled:

```bash
export ECS_EXEC_TASK_ARN=$(aws ecs list-tasks --cluster retail-store-ecs-cluster \
  --service-name ui --query 'taskArns[0]' --output text | \
  xargs -n1 aws ecs describe-tasks --cluster retail-store-ecs-cluster --tasks | \
  jq -r '.tasks[] | select(.enableExecuteCommand == true) | .taskArn' | \
  head -n 1)

echo ${ECS_EXEC_TASK_ARN}
```

Copy the environment variable to the running task:

```bash
aws ecs execute-command --cluster retail-store-ecs-cluster \
  --task $ECS_EXEC_TASK_ARN \
  --container application \
  --interactive \
  --command "/bin/bash -c 'echo UI_TLS_PRIVATE_IP=$(echo $UI_TLS_PRIVATE_IP) > /app/private_ip.env'"
```

Start an interactive `/bin/bash` session in the running task:

```bash
aws ecs execute-command --cluster retail-store-ecs-cluster \
  --task $ECS_EXEC_TASK_ARN \
  --container application \
  --interactive \
  --command "/bin/bash"
```

---

### Set up the environment

Load the environment variable into the current session:

```bash
source /app/private_ip.env
```

Install the `openssl` command on the running container:

```bash
yum install openssl -y
```

---

### Verify the TLS Certificate on the `ui-tls` task

Run the following command to retrieve and review the certificate:

```bash
openssl s_client -connect $UI_TLS_PRIVATE_IP:8080 < /dev/null 2> /dev/null \
  | openssl x509 -noout -text
```

Example output:

```
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number: 11:11:11:11:11:11:11:11:11:11:11:11:11:11:11:11
        Signature Algorithm: sha256WithRSAEncryption
        Issuer: C = US, O = ECS Immersion Day, OU = Workshop
        Validity
            Not Before: Aug 18 13:55:27 2024 GMT
            Not After : Aug 25 14:55:27 2024 GMT
        Subject: OU = Workshop, O = ECS Immersion Day, C = US
        Subject Public Key Info:
            Public Key Algorithm: id-ecPublicKey
                Public-Key: (256 bit)
                pub:
                    04:85:6e:c0:43:44:f9:e9:7d:ba:ec:6c:fe:0e:37:...
                    53:82:c2:a2:20
                ASN1 OID: prime256v1
                NIST CURVE: P-256
        X509v3 extensions:
            X509v3 Subject Alternative Name:
                DNS:ui-tls.retailstore.local
            X509v3 Basic Constraints:
                CA:FALSE
            X509v3 Authority Key Identifier:
                11:11:11:11:11:11:11:11:11:11:11:11:11:11:11:11:11:11:11:11
            X509v3 Subject Key Identifier:
                11:11:11:11:11:11:11:11:11:11:11:11:11:11:11:11:11:11:11:11
            X509v3 Key Usage: critical
                Digital Signature, Key Encipherment
            X509v3 Extended Key Usage:
                TLS Web Server Authentication, TLS Web Client Authentication
    Signature Algorithm: sha256WithRSAEncryption
    Signature Value:
        a7:dc:2e:28:9b:ae:2d:93:de:05:93:56:e3:c8:81:50:fe:3f:...
        70:92:7b:c1:b7:16:9b:8a:16:61:c1:aa:ab:9e:de:d3:e4:e7:
        cb:05:86:00
```

To terminate your exec session:

```bash
exit
```