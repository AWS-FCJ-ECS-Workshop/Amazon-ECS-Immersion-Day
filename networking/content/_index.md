---
title : "Amazon ECS Series: Networking"
date :  "`r Sys.Date()`" 
chapter : false
---

# Networking

Container-based microservices applications consist of multiple distributed components that need to communicate with each other. In our sample application, the user interface component communicates with the Catalog service through APIs, which then interfaces with a MySQL database for persistent storage, as illustrated in the architecture diagram below.

![ECS Microservices Architecture](/images/image.png)
*Figure 1. ECS Microservices Architecture*

This chapter explores essential Amazon ECS networking concepts specific to AWS Fargate. Understanding these networking fundamentals is critical for architecting and deploying containerized applications effectively on AWS.

Learn about:

*   [📘 Prerequisites](1-prerequisites/)
*   [📘 Amazon ECS Network Mode](2-network-mode/)
*   [📘 ECS Service Connect](3-service-connect/)
*   [📘 Amazon ECS Service Connect with TLS](4-service-connect-with-tls/)

These sections will guide you through the key networking components and configurations required for robust container communication in Amazon ECS with AWS Fargate.