---
title : "Service Auto Scaling"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre: "<b> 2. </b>"
---

#### Service Auto Scaling

Amazon ECS integrates with Amazon CloudWatch to scale ECS services based on real-time metrics. ECS transmits metrics to CloudWatch every minute, enabling precise monitoring and scaling decisions. When metrics exceed defined thresholds in your scaling policy, CloudWatch alarms trigger adjustments to the desired task count in your service. This process automatically increases capacity during high demand and decreases it when demand drops.

![Demonstrating Service Auto Scaling Work](/images/2-service-auto-scaling/image.png)
*Figure 1: Service Auto Scaling workflow*

Amazon ECS provides three service scaling strategies:

![Three type of scaling strategies](/images/2-service-auto-scaling/image-1.png)
*Figure 2: ECS scaling strategy types*

**Target Tracking Scaling**
- Maintains a specified metric at a target value by automatically adjusting task count
- Requires minimal configuration and maintenance
- Recommended for most production workloads
- Example: Maintaining average CPU utilization at 50%

**Step Scaling**
- Provides granular control over scaling actions
- Allows custom metric selection and threshold values
- Enables specific resource adjustments at each step
- Configurable breach evaluation periods for metric alarms
- Suitable for complex scaling requirements

**Scheduled Scaling**
- Scales resources based on predictable load patterns
- Ideal for applications with known traffic variations
- Enables proactive resource allocation
- Example: Increasing capacity before daily peak hours
- Reduces costs by scaling down during low-demand periods

Each scaling strategy helps optimize resource utilization and application performance while managing costs effectively. Choose the appropriate strategy based on your workload patterns and operational requirements.