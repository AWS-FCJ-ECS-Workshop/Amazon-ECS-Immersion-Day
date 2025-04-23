---
title: "Cloud Map - Service Discovery"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: "<b> 2. </b>"
---

![Cloud map icon](/images/3-service-connect/image.png?width=1pc)
*Figure 1. Cloud Map Icon*

### What is AWS Cloud Map?

AWS Cloud Map is a fully managed service discovery solution that enables you to map logical names to your application's backend services and resources. It helps your applications locate dependencies through a unified service registry.

### AWS Cloud Map Components

#### Namespace

A namespace provides a unique scope for your resources and defines how you want to discover them. It acts as a container for your services and determines the discovery method - either through DNS queries, API calls, or both.

#### Service 

After creating a namespace, you define services to represent each type of resource you want to discover. For example, you might create separate services for web servers, application servers, and database servers.

#### Service Instance

Service instances are the actual resources registered with AWS Cloud Map. When your application adds a resource, it calls the RegisterInstance API to create a service instance within a service. This enables other applications to discover and connect to these resources.

### Workshop Use Case

In this workshop, we will use AWS Cloud Map to implement service discovery for three microservices: UI service, catalog service, and assets service. This demonstrates Cloud Map's service discovery capabilities in a microservices architecture.

#### Create a Service Discovery Namespace

1. Navigate to [AWS Cloud Map Namespaces console](https://console.aws.amazon.com/cloudmap/home/namespaces) and select **Create Namespace**

![Cloud Map Namespaces Console](/images/1-prerequisites/2-service-discovery/image.png)

*Figure 2. Cloud Map Namespaces Console*

2. Configure the namespace with these settings:
   - Namespace name: `retailstore.local`
   - Instance discovery: `API calls and DNS Queries in VPCs`
   - VPC: `ecs-lab-vpc`

![Create Namespace Configuration](/images/1-prerequisites/2-service-discovery/image-1.png)
*Figure 3. Create Namespace Configuration*

3. Verify successful namespace creation:

![Successful Namespace Creation](/images/1-prerequisites/2-service-discovery/image-2.png)
*Figure 4. Successful Namespace Creation*

✅ You have successfully created a Cloud Map namespace