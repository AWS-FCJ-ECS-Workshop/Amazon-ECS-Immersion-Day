---
title: "Prerequisites"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: "<b> 2. </b>"
---

Before starting the lab, we need to **create essential AWS resources** for the Cart service:

![alt text](/images/2.2-create-dynaodb/ECS-Lab-Networking-Prerequisites.png)

1. **Cart Security Group** – This Security Group will allow traffic through the necessary ports for the **UI Service**, ensuring proper routing to our cart-related services.

2. **Serverless DynamoDB** – A fully managed, serverless database to **store and retrieve cart details**, enabling scalable and low-latency access.

---

#### Key Setup Steps

- [Create Cart Security Group](2.1-cart-security-group/) - Set up ingress rules to expose required ports for the UI Service.

- [Create Serverless DynamoDB](2.2-create-dynamodb/) - Provision a DynamoDB table for storing cart item data in a scalable, pay-per-request manner.
