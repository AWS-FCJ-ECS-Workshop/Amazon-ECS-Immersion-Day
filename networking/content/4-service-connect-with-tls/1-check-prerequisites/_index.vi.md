---
title: "Kiểm tra Điều kiện tiên quyết"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: "<b> 4.1 </b>"
---

Phần này đề cập đến việc thiết lập ECS Service Connect với mã hóa TLS cho cả lưu lượng mạng đến và đi. Để biết thông tin chi tiết, tham khảo [tài liệu ECS Service Connect TLS](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/service-connect-tls.html).

Chúng ta sẽ xác minh và cấu hình các tài nguyên sau:

1. Cấu hình AWS Private Certificate Authority
2. Thiết lập Application Load Balancer
3. IAM Role cần thiết để cấp chứng chỉ

#### Xem xét AWS Private Certificate Authority hiện có

Xác minh cấu hình của AWS Private Certificate Authority đã được tạo trước:

```bash
CERTIFICATE_AUTH_ARN=$(aws acm-pca list-certificate-authorities \
    --query 'CertificateAuthorities[?Status==`ACTIVE` && CertificateAuthorityConfiguration.Subject.Organization==`ECS Immersion Day`].Arn' \
    --output text)

aws acm-pca describe-certificate-authority --certificate-authority-arn=$CERTIFICATE_AUTH_ARN
```

{{% expand "Mở rộng để xem chi tiết về private certificate authority" %}}
AWS Private Certificate Authority hỗ trợ hai chế độ hoạt động:

1. Mục đích chung: Ngày hết hạn chứng chỉ có thể cấu hình
2. Ngắn hạn: Chứng chỉ có hiệu lực tối đa bảy ngày

Mặc dù Amazon ECS hỗ trợ cả hai chế độ, chứng chỉ ngắn hạn được khuyến nghị. Tìm hiểu thêm trong [tài liệu về chứng chỉ Service Connect TLS](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/service-connect-tls.html#service-connect-tls-certificates).

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

#### Xem xét Cấu hình Application Load Balancer hiện có

Kiểm tra các tài nguyên ALB đã được cấu hình trước, bao gồm HTTPS listener, target group và security group. Xem xét cấu hình Target Group **ui-application-tls**:

```bash
export UI_TARGET_GROUP_TLS_ARN=$(aws elbv2 describe-target-groups --names ui-application-tls \
 --query 'TargetGroups[0].TargetGroupArn' --output text)

aws elbv2 describe-target-groups --target-group-arns $UI_TARGET_GROUP_TLS_ARN
```

{{% expand "Mở rộng để xem chi tiết target group" %}}
Target Group **ui-application-tls** sử dụng HTTPS trên cổng 8080, khớp với cổng container của UI Service cho giao tiếp Service Connect proxy.

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

Cấu hình HTTPS listener cho ALB **retail-store-ecs-ui** với hỗ trợ TLS 1.3:

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

{{% expand "Mở rộng để xem chi tiết listener" %}}
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

#### Tạo IAM Role mới cho Chứng chỉ ECS Service Connect

Tạo một IAM role với các quyền cần thiết để cấp chứng chỉ. Amazon ECS cung cấp một chính sách được quản lý xác định các quyền này. Để biết chi tiết, xem [AmazonECSInfrastructureRolePolicyForServiceConnectTransportLayerSecurity](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/security-iam-awsmanpol.html#security-iam-awsmanpol-AmazonECSInfrastructureRolePolicyForServiceConnectTransportLayerSecurity).

Tạo trust policy:

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

Tạo và cấu hình IAM role:

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