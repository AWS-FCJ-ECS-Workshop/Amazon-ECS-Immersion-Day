---
title: "ECS Service Connect"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: "<b> 3. </b>"
---

{{% notice info %}}
Complete the [Fundamentals](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/fundamentals/) chapter before proceeding with this lab.
{{% /notice %}}

![alt text](/images/3-service-connect/image.png?width=15pc)
*Figure 1. AWS Cloud Map*

**Amazon ECS Service Connect** provides a streamlined solution for service-to-service communication within your containerized applications. It enables seamless service discovery, connectivity, and traffic monitoring capabilities. Using Service Connect, your applications can communicate through simple service names and standard ports across:

- Services within the same ECS cluster
- Services in different clusters 
- Services across VPCs within the same AWS Region

[Learn more about Service Connect in the AWS documentation.](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/networking-connecting-services.html#networking-connecting-services-serviceconnect)

Amazon ECS offers additional options for configuring service-to-service communication:

* [Internal Application Load Balancer](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/networking-connecting-services.html#networking-connecting-services-elb) - For load-balanced communication between services
* [AWS Cloud Map Service Discovery](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/networking-connecting-services.html#networking-connecting-services-direct) - For DNS-based service discovery
* [Amazon VPC Lattice](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-vpc-lattice.html) - For application networking across VPCs and accounts