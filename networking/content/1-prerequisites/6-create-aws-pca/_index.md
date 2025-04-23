---
title: "Create AWS Private Certificate Authority"
date: "`r Sys.Date()`"
weight: 6
chapter: false
pre: "<b> 6. </b>"
---

#### What is AWS Private Certificate Authority?

AWS Private Certificate Authority (AWS Private CA) is a managed service that lets you securely issue and manage private certificates for your connected resources from a central location.

#### Use Case

Enable Transport Layer Security (TLS) for Application Load Balancer.

#### Creating an AWS Private CA

Navigate to [AWS Private Certificate Authority console](https://console.aws.amazon.com/acm-pca/home/certificateAuthorities)

1. Select **Create a private CA**

Configure the following settings:
- Mode: `Short-lived certificate`
- CA type: `Root`
- Organization: `ECS Immersion Day`
- Organization Unit: `Workshop`
- Country Name: `United States (US)`

![alt text](/images/1-prerequisites/6-create-aws-pca/image.png)
*Figure 1: Creating AWS Private CA*

{{% notice note %}}
RSA 2048-bit provides enhanced online security for your applications
{{% /notice %}}

- Select **RSA 2048**

![alt text](/images/1-prerequisites/6-create-aws-pca/image-1.png)
*Figure 2: Key algorithm selection*

Add tags for resource management

![alt text](/images/1-prerequisites/6-create-aws-pca/image-2.png)
*Figure 3: Tag management*

After successful Private CA creation:
1. Select **Action** dropdown
2. Choose **Install CA Certificate**

![alt text](/images/1-prerequisites/6-create-aws-pca/image-3.png)
*Figure 4: Private CA creation confirmation*

In the Install CA Certificate interface:
- Select signature algorithm: `SHA256 RSA`

![alt text](/images/1-prerequisites/6-create-aws-pca/image-4.png)
*Figure 5: Install CA Certificate configuration*

After successful installation, request a certificate:
1. Navigate to [AWS Certificate Manager](https://console.aws.amazon.com/acm/home/welcome)
2. Select **Request a Certificate**

![alt text](/images/1-prerequisites/6-create-aws-pca/image-5.png)
*Figure 6: AWS Certificate Manager (ACM) console*

In the Request Certificate interface:
1. Select **Request a private certificate**
2. Click **Next**

![alt text](/images/1-prerequisites/6-create-aws-pca/image-6.png)
*Figure 7: Certificate request selection*

Configure the private certificate:
1. Select your newly created CA
2. Enter domain name
3. Select Key algorithm: `RSA 2048`

![alt text](/images/1-prerequisites/6-create-aws-pca/image-7.png)
*Figure 8: Private Certificate configuration*

Certificate request confirmation

![alt text](/images/1-prerequisites/6-create-aws-pca/image-8.png)
*Figure 9: Successful private certificate request*

Set the environment variable:

```bash
export ALB_CERTIFICATE_ARN=arn:aws:acm:ap-southeast-2:XXXXXXXXXXX:certificate/1c018d88-4404-48f5-8585-d460ed8c22ba
```