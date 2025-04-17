---
title: "Prerequisites"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: "<b> 2. </b>"
---

To implement the Cart service, you must first create the following AWS resources:

![alt text](/images/2-prerequisites/ECS-Lab-Networking-Prerequisites.png)

1. **Cart Security Group** – A security group that controls inbound traffic from the UI Service to the Cart service through specified ports.

2. **DynamoDB Table** – A serverless, fully managed NoSQL database that stores cart data with automatic scaling capabilities.

#### Key Setup Steps

- [Create Cart Security Group](2.1-cart-security-group/) - Configure inbound rules to allow traffic from the UI Service.

- [Create DynamoDB Table](2.2-create-dynamodb/) - Set up a DynamoDB table with on-demand capacity mode for cart data storage.