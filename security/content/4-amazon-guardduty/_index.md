---
title : "Amazon GuardDuty"
date :  "`r Sys.Date()`" 
weight : 4
chapter : false
pre: "<b> 4. </b>"
---

{{% notice info %}}
Complete the [Fundamentals](/Amazon-ECS-Immersion-Day/fundamentals/) chapter before proceeding with this lab.
{{% /notice %}}

[Amazon GuardDuty](https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_settingup.html) is a continuous security monitoring service that analyzes and processes data sources to identify threats to your AWS accounts and workloads. It provides runtime monitoring capabilities specifically designed for ECS clusters and containers, detecting potential security threats and unauthorized activities.

This chapter guides you through:

- Enabling and configuring GuardDuty for your AWS environment
- Integrating GuardDuty with your ECS clusters
- Monitoring ECS-specific security events including:
  - Suspicious API activity
  - Potentially compromised container instances
  - Communication with known malicious IP addresses
  - Anomalous container behavior

Upon completion, you will have implemented a comprehensive threat detection system for your ECS workloads. You will understand how to:

- Monitor GuardDuty findings through the AWS Management Console
- Interpret security alerts related to your containerized applications
- Take appropriate remediation actions based on GuardDuty findings
- Configure automated responses to security events

![alt text](/images/4-amazon-guardduty/image.png)
*Figure 1: Amazon GuardDuty monitoring ECS tasks for security threats*