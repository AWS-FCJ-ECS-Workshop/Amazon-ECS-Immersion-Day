---
title: "Check Prerequisite"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: "<b> 4.1 </b>"
---

This section covers setting up ECS Service Connect with TLS encryption for both inbound and outbound network traffic. For detailed information, refer to the [ECS Service Connect TLS documentation](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/service-connect-tls.html).

We will verify and configure the following resources:

1. AWS Private Certificate Authority configuration
2. Application Load Balancer setup
3. Required IAM Role for certificate issuance

#### Review the existing AWS Private Certificate Authority

Verify the configuration of the pre-created AWS Private Certificate Authority:

```bash
CERTIFICATE_AUTH_ARN=$(aws acm-pca list-certificate-authorities \
    --query 'CertificateAuthorities[?Status==`ACTIVE` && CertificateAuthorityConfiguration.Subject.Organization==`ECS Immersion Day`].Arn' \
    --output text)

aws acm-pca describe-certificate-authority --certificate-authority-arn=$CERTIFICATE_AUTH_ARN
```

{{% expand "Expand for private certificate authority details" %}}
AWS Private Certificate Authority supports two operational modes:

1. General purpose: Configurable certificate expiration dates
2. Short-lived: Maximum seven-day certificate validity

While Amazon ECS supports both modes, short-lived certificates are recommended. Learn more in the [Service Connect TLS certificates documentation](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/service-connect-tls.html#service-connect-tls-certificates).

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

#### Review the existing Application Load Balancer Configuration

Examine the pre-configured ALB resources, including the HTTPS listener, target group, and security group. Review the **ui-application-tls** Target Group configuration:

```bash
export UI_TARGET_GROUP_TLS_ARN=$(aws elbv2 describe-target-groups --names ui-application-tls \
 --query 'TargetGroups[0].TargetGroupArn' --output text)

aws elbv2 describe-target-groups --target-group-arns $UI_TARGET_GROUP_TLS_ARN
```

{{% expand "Expand for target group details" %}}
The **ui-application-tls** Target Group uses HTTPS on port 8080, matching the UI Service container port for Service Connect proxy communication.

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

Configure the HTTPS listener for the **retail-store-ecs-ui** ALB with TLS 1.3 support:

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

{{% expand "Expand for listener details" %}}

In the following output, you can review that the HTTPS listener is configured on the standard HTTPS port 443, and the SSL Policy selected is TLS 1.3.

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

#### Create a new IAM Role for ECS Service Connect Certificates

Create an IAM role with the required permissions for certificate issuance. Amazon ECS provides a managed policy that defines these permissions. For details, see [AmazonECSInfrastructureRolePolicyForServiceConnectTransportLayerSecurity](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/security-iam-awsmanpol.html#security-iam-awsmanpol-AmazonECSInfrastructureRolePolicyForServiceConnectTransportLayerSecurity).

Create the trust policy:

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

Create and configure the IAM role:

```bash
aws iam create-role \
    --role-name ecs-service-connect-certificate-role \
    --assume-role-policy-document file://ecs-service-connect-certificate-policy.json \
    --query Role.Arn \
    --output text

aws iam attach-role-policy \
     --role-name ecs-service-connect-certificate-role \
     --policy-arn arn:aws:iam::aws:policy/service-role/AmazonECSInfrastructureRolePolicyForServiceConnectTransportLayerSecurity
```