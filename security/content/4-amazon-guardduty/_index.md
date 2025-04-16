---
title : "Amazon GuardDuty"
date :  "`r Sys.Date()`" 
weight : 4
chapter : false
pre: "<b> 4. </b>"
---


{{% notice info %}}
You must have completed the following chapters as pre-requisites for this lab: [Fundamentals](/Amazon-ECS-Immersion-Day/fundamentals/)
{{% /notice %}}

[Amazon GuardDuty](https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_settingup.html) is a threat detection service that continuously monitors for malicious or unauthorized behavior in your AWS accounts and workloads. This powerful tool can be particularly valuable for runtime monitoring of ECS clusters and containers, helping to detect potential security issues or compromised instances.

In this chapter, you will explore the process of enabling GuardDuty and integrating it with your ECS clusters. You'll learn how to set up monitoring for suspicious API calls, potentially compromised instances, and communications with known malicious IP addresses.

By the conclusion of this chapter, you will have established a robust runtime threat monitoring and alerting system for your ECS workloads using GuardDuty. You'll gain the knowledge and skills necessary to effectively respond to and resolve GuardDuty findings through the AWS console, enhancing the security posture of your containerized applications.

![alt text](/images/4-amazon-guardduty/image.png)
*Figure 1. Amazon Guarduty monitors ECS Tasks*