---
title: "Amazon ECS Service Connect with TLS"
date: "`r Sys.Date()`"
weight: 4
chapter: false
pre: "<b> 4. </b>"
---

{{% notice info %}}
Complete these prerequisites before starting this lab: [Fundamentals](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/fundamentals/), [ECS Service Connect](../3-service-connect/)
{{% /notice %}}

Amazon ECS Service Connect provides built-in traffic encryption using Transport Layer Security (TLS) certificates for ECS services. When you configure your ECS services with AWS Private Certificate Authority (AWS Private CA), ECS automatically manages TLS certificates to encrypt traffic between Service Connect-enabled services. This includes:

- Certificate provisioning
- Certificate rotation 
- Certificate distribution
- Traffic encryption between services

In this lab, you will:

- Deploy a TLS-enabled version of the UI service (`ui-tls`)
- Configure TLS encryption for the existing Catalog and Assets services  
- Validate TLS connectivity between services

[Learn more about ECS Service Connect TLS](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/service-connect-tls.html)