---
title: "Advanced TLS Connect"
date: "`r Sys.Date()`"
weight: 4
chapter: false
pre: "<b> 3.4. </b>"
---

In this section, we will explore the ECS Service Connect capabilities by initiating an interactive session on the running containers to better understand the Service Connect configuration and capabilities.

#### Connect to the ECS Task

Execute the following command to select one of the running tasks with `enableExecuteCommand` enabled:

```bash
    ECS_EXEC_TASK_ARN=$(aws ecs list-tasks --cluster retail-store-ecs-cluster \
        --service-name ui --query 'taskArns[]' --output text | \
        xargs -n1 aws ecs describe-tasks --cluster retail-store-ecs-cluster --tasks | \
        jq -r '.tasks[] | select(.enableExecuteCommand == true) | .taskArn' | \
        head -n 1)
    
    echo $ECS_EXEC_TASK_ARN
```

This will output the ARN of the task:

```
    arn:aws:ecs:us-west-2:XXXXXXXXXX:task/retail-store-ecs-cluster/0564778486a846599b8bd6b544e5f6eb
```

Now, start a `/bin/bash` interactive session in the running task:

```bash
    if [ -z ${ECS_EXEC_TASK_ARN} ]; then echo "ECS_EXEC_TASK_ARN is not correctly configured!"; else
    aws ecs execute-command --cluster retail-store-ecs-cluster \
        --task $ECS_EXEC_TASK_ARN \
        --container application \
        --interactive \
        --command "/bin/bash"
    fi
```

#### Setup environment

In the Bash shell, install the `jq` command on the running container to format the JSON output on the command line:

```bash
    yum install jq -y
```

#### Explore the host file

The `/etc/hosts` file provides the mapping of fully qualified domain names (FQDNs) to their respective IP addresses. Review the content of the `/etc/hosts` file by running the following command:

```bash
    cat /etc/hosts
```

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
    

The file contains the three **Discovery names** set up in the ECS Namespace with three different local IPs `127.255.0.X` (IPv4 and IPv6). These local IPs all point to the same ECS local proxy, which will redirect the traffic to the appropriate ECS remote proxy bound to the specific service.


#### Test the connection to the Catalog service

Test the connection to the Catalog API from the running container:

```bash
    curl http://catalog/catalogue/tags | jq

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

To terminate your `exec` session, run:

    exit
