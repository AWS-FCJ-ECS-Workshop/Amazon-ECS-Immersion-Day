---
title: "Amazon ECS Service Connect with TLS"
date: "`r Sys.Date()`"
weight: 4
chapter: false
pre: "<b> 4. </b>"
---

{{% notice info %}}
You must have completed the following chapters as pre-requisites for this lab: [Fundamentals](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/fundamentals/), [ECS Service Connect](../3-service-connect/)
{{% /notice %}}


Amazon ECS Service Connect supports automatic traffic encryption using _Transport Layer Security (TLS)_ certificates for Amazon ECS services. By configuring your Amazon ECS services to use an [AWS Private Certificate Authority (AWS Private CA)](https://docs.aws.amazon.com/privateca/latest/userguide/PcaWelcome.html) , Amazon ECS automatically provisions TLS certificates to encrypt traffic between your Amazon ECS Service Connect services. Amazon ECS handles the generation, rotation, and distribution of TLS certificates used for traffic encryption. [You can find more information here.](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/service-connect-tls.html) 

In the following sections, you will:

*   Deploy a new version of the `ui` service with TLS enabled, called the `ui-tls` service
*   Enable TLS for the existing `Catalog` and `Assets` services
*   Verify that TLS is enabled for Service Connect