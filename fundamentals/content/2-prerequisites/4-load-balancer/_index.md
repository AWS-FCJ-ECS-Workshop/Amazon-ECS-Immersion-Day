---
title: "Load Balancer"
date: "`r Sys.Date()`"
weight: 4
chapter: false
pre: "<b> 2.4. </b>"
---

### What is Load Balancing?

Load Balancing is a method of distributing network traffic evenly across a group of resources serving an application. In today's digital age, applications need to serve millions of simultaneous users and distribute various types of content like text, video, and images quickly and reliably.

To handle large volumes of traffic, applications are typically deployed across multiple servers with replicated data. The load balancer acts as an intermediary between users and the server group, ensuring fair and efficient distribution of load to all servers.

![Load balancer](/images/2-prerequisites/4-load-balancer/ECS-Lab-Networking-LoadBalancer.png)

### Types of Load Balancers

AWS provides the following types of load balancers based on how they inspect and route traffic:

#### 1. Application Load Balancer (ALB)
- Operates at Layer 7 of the OSI model
- Analyzes request content such as HTTP headers or SSL session ID
- Suitable for complex web applications with multiple components
- Supports intelligent content-based routing
#### 2. Network Load Balancer (NLB)
- Operates at Layer 4 of the OSI model
- Processes traffic based on network information (IP, port)
- Provides static IP addresses for servers
- Suitable for applications requiring high performance
#### 3. Global Server Load Balancer
- Distributes traffic across multiple geographic regions
- Routes requests to servers closest to users
- Supports failover between regions
- Optimizes global user experience
#### 4. DNS Load Balancer
- Load balancing through DNS system
- Routes requests across resource groups on a domain
- Supports global traffic distribution
- Ensures high availability for applications

### Practice Creating a Load Balancer

In this practice section, we will create an Application Load Balancer to distribute traffic for the application.

#### 1. Create ALB Target Group

Target Group defines the destinations (targets) that the Load Balancer will forward traffic to. Targets can be EC2 instances, IP addresses, or Lambda functions.

#### Implementation steps:

1. Access [Target Group Dashboard](console.aws.amazon.com/ec2/home#TargetGroups)
2. Configure Target Group:
   - Target type: IP Addresses
   - Name: `ui-application`
   - Port: HTTP 8080
   - VPC: `ecs-lab-vpc`
   - Health check path: `/actuator/health` (Path from application)

![Target Group Configuration](/images/2-prerequisites/4-load-balancer/image-1.png)

![Health Check Configuration](/images/2-prerequisites/4-load-balancer/image-2.png)

![Target Group Creation Success](/images/2-prerequisites/4-load-balancer/image-7.png)

#### 2. Create Application Load Balancer

1. Access [Load Balancer Dashboard](console.aws.amazon.com/ec2/home#LoadBalancers)
2. Select Application Load Balancer

![Select ALB Type](/images/2-prerequisites/4-load-balancer/image-3.png)

3. Basic configuration:
   - Name the Load Balancer
   - Scheme: Internet-facing

![ALB Basic Configuration](/images/2-prerequisites/4-load-balancer/image-5.png)

   - Network: Select VPC and public subnets
   - Security Group: `ecs-lab-public-sg` (allows inbound HTTP 80 & HTTPS 443)

![Network Configuration](/images/2-prerequisites/4-load-balancer/image-6.png)

   - Listener: HTTP 80 forward to target group `ui-application`
![Listener Configuration](/images/2-prerequisites/4-load-balancer/image-8.png)

![ALB Creation Success](/images/2-prerequisites/4-load-balancer/image-12.png)

### Conclusion

In this section, you have learned how to:
- Create Target Groups to define destinations for traffic
- Set up Application Load Balancer to distribute traffic
- Configure necessary components like security groups and listeners