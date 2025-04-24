---
title: "Advanced TLS Connect"
date: "`r Sys.Date()`"
weight: 4
chapter: false
pre: "<b> 3.4. </b>"
---

In this section, you will explore ECS Service Connect capabilities by establishing interactive sessions with running containers to understand the Service Connect configuration and functionality.

#### Connect to the ECS Task

Run the following command to identify a running task that has `enableExecuteCommand` enabled:

```bash
ECS_EXEC_TASK_ARN=$(aws ecs list-tasks --cluster retail-store-ecs-cluster \
    --service-name ui --query 'taskArns[]' --output text | \
    xargs -n1 aws ecs describe-tasks --cluster retail-store-ecs-cluster --tasks | \
    jq -r '.tasks[] | select(.enableExecuteCommand == true) | .taskArn' | \
    head -n 1)

echo $ECS_EXEC_TASK_ARN
```

The command outputs the task ARN:

```
arn:aws:ecs:us-west-2:XXXXXXXXXX:task/retail-store-ecs-cluster/0564778486a846599b8bd6b544e5f6eb
```

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

#### Configure the Environment

Install the `jq` command-line JSON processor to format JSON output:

```bash
yum install jq -y
```

#### Examine Host File Configuration

View the `/etc/hosts` file to see the mapping between fully qualified domain names (FQDNs) and IP addresses:

```bash
cat /etc/hosts
```

Output:
```
127.0.0.1 localhost
X.X.X.X ip-X-X-X-X.us-west-2.compute.internal
127.255.0.1 assets
2600:f0f0:0:0:0:0:0:1 assets
127.255.0.2 catalog
2600:f0f0:0:0:0:0:0:2 catalog
127.255.0.3 ui
2600:f0f0:0:0:0:0:0:3 ui
```

The file shows three Discovery names configured in the ECS Namespace, each with unique local IPv4 (`127.255.0.X`) and IPv6 addresses. These addresses route to the ECS local proxy, which directs traffic to the appropriate ECS remote proxy for each service.

#### Verify Catalog Service Connectivity

Test the connection to the Catalog API from within the container:

```bash
curl http://catalog/catalogue/tags | jq
```

Expected output:
```json
[
  {
    "name": "smart",
    "displayName": "Smart"
  },
  {
    "name": "dress",
    "displayName": "Dress"
  },
  {
    "name": "luxury",
    "displayName": "Luxury"
  },
  {
    "name": "casual",
    "displayName": "Casual"
  }
]
```

To end your `exec` session:
```bash
exit
```