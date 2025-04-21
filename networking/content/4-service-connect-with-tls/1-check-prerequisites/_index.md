---
title: "Check Prerequisite"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: "<b> 4.1 </b>"
---

In this section, we will review and create the necessary AWS resources to set up ECS Service Connect with TLS, ensuring encryption for both inbound and outbound network traffic managed by ECS Service Connect. [For more information, click here](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/service-connect-tls.html) .

We will check each resource with the following steps:

*   Review the existing [AWS Private Certificate Authority](https://docs.aws.amazon.com/privateca/latest/userguide/PcaWelcome.html) .
*   Review the existing Application Load Balancer Configuration.
*   Create a new IAM Role required to issue certificates used by ECS Service Connect.

#### Review the existing AWS Private Certificate Authority

Let's review the configuration of the existing AWS Private Certificate Authority. We've created this in advance for a smoother hands-on experience.

```bash
    CERTIFICATE_AUTH_ARN=$(aws acm-pca list-certificate-authorities \
        --query 'CertificateAuthorities[?Status==`ACTIVE` && CertificateAuthorityConfiguration.Subject.Organization==`ECS Immersion Day`].Arn' \
        --output text)
    
    aws acm-pca describe-certificate-authority --certificate-authority-arn=$CERTIFICATE_AUTH_ARN
```

{{% expand "Expand for exploring private certificate authority detail" %}}
AWS Private Certificate Authority can operate in two modes:

*   _General purpose_: Certificates that can be configured with any expiration date.
*   _Short-lived_: Certificates with a maximum validity of seven days.

While Amazon ECS supports both modes, we recommend using short-lived certificates. [For more information, click here](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/service-connect-tls.html#service-connect-tls-certificates) .

```json
    {
        "CertificateAuthority": {
            "Arn": "arn:aws:acm-pca:us-east-2:XXXXXXXXXXXX:certificate-authority/5230e14a-ac88-4dcd-be0e-7b35ff57d12a",
            "OwnerAccount": "XXXXXXXXXXXX",
            "CreatedAt": "2024-09-06T21:30:49.096000+00:00",
            "LastStateChangeAt": "2024-09-06T21:30:55.513000+00:00",
            "Type": "ROOT",
            "Serial": "200429104715365165680450616781560939935",
            "Status": "ACTIVE",
            "NotBefore": "2024-09-06T20:30:52+00:00",
            "NotAfter": "2026-09-06T21:30:52+00:00",
            "CertificateAuthorityConfiguration": {
                "KeyAlgorithm": "RSA_2048",
                "SigningAlgorithm": "SHA256WITHRSA",
                "Subject": {
                    "Country": "US",
                    "Organization": "ECS Immersion Day",
                    "OrganizationalUnit": "Workshop"
                }
            },
            "RevocationConfiguration": {
                "CrlConfiguration": {
                    "Enabled": false
                },
                "OcspConfiguration": {
                    "Enabled": false
                }
            },
            "KeyStorageSecurityStandard": "FIPS_140_2_LEVEL_3_OR_HIGHER",
            "UsageMode": "SHORT_LIVED_CERTIFICATE"
        }
    }
```

{{% /expand %}}

---

#### Review the existing Application Load Balancer Configuration

For convenience, we have pre-built resources related to the ALB (Application Load Balancer), including the listener, target group, and security group. By running the following command, we can review the details of the **ui-application-tls** Target Group associated with the HTTPS listener.

```bash
    export UI_TARGET_GROUP_TLS_ARN=$(aws elbv2 describe-target-groups --names ui-application-tls \
     --query 'TargetGroups[0].TargetGroupArn' --output text)
    
    aws elbv2 describe-target-groups --target-group-arns $UI_TARGET_GROUP_TLS_ARN
```


{{% expand "Expand for exploring target group detail" %}}

Below, you can review the details of the **ui-application-tls** Target Group. By default, the Service Connect proxy listens on the **containerPort** from the task definition port mapping. For this reason, as you can see from the output, the Target Group is configured to use **HTTPS** on port **8080**, which is the container port of the UI Service.

```json
    {
        "TargetGroups": [
            {
                "TargetGroupArn": "arn:aws:elasticloadbalancing:us-west-2:XXXXXXXXXXXX:targetgroup/ui-application-tls/b55c22b6ee1466f3",
                "TargetGroupName": "ui-application-tls",
                "Protocol": "HTTPS",
                "Port": 8080,
                "VpcId": "vpc-09a385263782f2c7d",
                "HealthCheckProtocol": "HTTPS",
                "HealthCheckPort": "8080",
                "HealthCheckEnabled": true,
                "HealthCheckIntervalSeconds": 30,
                "HealthCheckTimeoutSeconds": 5,
                "HealthyThresholdCount": 2,
                "UnhealthyThresholdCount": 3,
                "HealthCheckPath": "/actuator/health",
                "Matcher": {
                    "HttpCode": "200"
                },
                "LoadBalancerArns": [
                    "arn:aws:elasticloadbalancing:us-west-2:XXXXXXXXXXXX:loadbalancer/app/retail-store-ecs-ui/bccc3270b65387b4"
                ],
                "TargetType": "ip",
                "ProtocolVersion": "HTTP1",
                "IpAddressType": "ipv4"
            }
        ]
    }
```
{{% /expand %}}

---

Now, you need to connect the Target Group to an HTTPS listener of the **retail-store-ecs-ui** Application Load Balancer (ALB). When creating the HTTPS Listener, since ECS Service Connect only supports TLS 1.3, you will need to specify the correct SSL Policy.

To create the HTTPS listener, run the following command:

```bash
    RETAIL_ALB_ARN=$(aws elbv2 describe-load-balancers \
         --name retail-store-ecs-ui \
         --query 'LoadBalancers[0].LoadBalancerArn' \
         --output text)
    
    aws elbv2 create-listener \
        --load-balancer-arn $RETAIL_ALB_ARN \
        --default-action '[{"Type": "forward", "TargetGroupArn": "'$UI_TARGET_GROUP_TLS_ARN'"}]' \
        --certificates CertificateArn=$ALB_CERTIFICATE_ARN \
        --protocol HTTPS \
        --port 443 \
        --ssl-policy ELBSecurityPolicy-TLS13-1-2-2021-06
```
    

{{% expand "Expand to explore listener details" %}}

In the following output, you can review that the HTTPS listener is configured on the standard **HTTPS** port **443**, and the **SSL Policy** selected is **TLS 1.3**.

```json
    {
        "Listeners": [
            {
                "ListenerArn": "arn:aws:elasticloadbalancing:us-east-2:XXXXXXXXXXXX:listener/app/retail-store-ecs-ui/eb06fe1626ab7552/5980f05aff20af5e",
                "LoadBalancerArn": "arn:aws:elasticloadbalancing:us-east-2:XXXXXXXXXXXX:loadbalancer/app/retail-store-ecs-ui/eb06fe1626ab7552",
                "Port": 443,
                "Protocol": "HTTPS",
                "Certificates": [
                    {
                        "CertificateArn": "arn:aws:acm:us-east-2:XXXXXXXXXXXX:certificate/afffd137-ad34-4bae-8b99-562990c1f18c"
                    }
                ],
                "SslPolicy": "ELBSecurityPolicy-TLS13-1-2-2021-06",
                "DefaultActions": [
                    {
                        "Type": "forward",
                        "TargetGroupArn": "arn:aws:elasticloadbalancing:us-east-2:XXXXXXXXXXXX:targetgroup/ui-application-tls/201e43a310c03184",
                        "ForwardConfig": {
                            "TargetGroups": [
                                {
                                    "TargetGroupArn": "arn:aws:elasticloadbalancing:us-east-2:XXXXXXXXXXXX:targetgroup/ui-application-tls/201e43a310c03184",
                                    "Weight": 1
                                }
                            ],
                            "TargetGroupStickinessConfig": {
                                "Enabled": false
                            }
                        }
                    }
                ]
            }
        ]
    }
```

{{% /expand %}}

---

#### Create a new IAM Role required to issue certificates used by ECS Service Connect

Additional IAM permissions are required to issue certificates. Amazon ECS provides a managed resource trust policy that outlines the set of permissions. For more information about this policy, see [AmazonECSInfrastructureRolePolicyForServiceConnectTransportLayerSecurity](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/security-iam-awsmanpol.html#security-iam-awsmanpol-AmazonECSInfrastructureRolePolicyForServiceConnectTransportLayerSecurity) .

```bash
    cat << EOF > ecs-service-connect-certificate-policy.json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Sid": "AllowAccessToECSForInfrastructureManagement",
                "Effect": "Allow",
                "Principal": {
                    "Service": "ecs.amazonaws.com"
                },
                "Action": "sts:AssumeRole"
            }
        ]
    }
    EOF
```

Run the following command to create a new **ecs-service-connect-certificate-role** role with the required managed policy:

```bash
    aws iam create-role \
        --role-name ecs-service-connect-certificate-role \
        --assume-role-policy-document file://ecs-service-connect-certificate-policy.json \
        --query Role.Arn \
        --output text
```
    
```bash
    aws iam attach-role-policy \
         --role-name ecs-service-connect-certificate-role \
         --policy-arn arn:aws:iam::aws:policy/service-role/AmazonECSInfrastructureRolePolicyForServiceConnectTransportLayerSecurity
```