---
title: "Triển khai UI Service với TLS"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: "<b> 4.2 </b>"
---

Phần này hướng dẫn bạn triển khai UI service với ECS Service Connect có bật TLS và cấu hình truy cập HTTPS.

#### Triển khai UI service với TLS được bật trong ECS Service Connect

Lấy ARN của ECS Service Connect role:

```bash
export ECS_SERVICE_CONNECT_ROLE_ARN=$(aws iam get-role --role-name ecs-service-connect-certificate-role --query Role.Arn --output text)
```

Triển khai ECS service với cấu hình TLS:

```bash
aws ecs create-service \
    --cluster retail-store-ecs-cluster \
    --service-name ui-tls \
    --task-definition retail-store-ecs-ui \
    --desired-count 1 \
    --enable-execute-command \
    --launch-type FARGATE \
    --load-balancers targetGroupArn=${UI_TARGET_GROUP_TLS_ARN},containerName=application,containerPort=8080 \
    --network-configuration "awsvpcConfiguration={subnets=[${PRIVATE_SUBNET1},${PRIVATE_SUBNET2}],securityGroups=[${UI_SG_ID}],assignPublicIp=DISABLED}" \
    --service-connect-configuration '{
        "enabled": true,
        "namespace": "retailstore.local",
        "services": [
            {
                "portName": "application",
                "discoveryName": "ui-tls",
                "clientAliases": [
                    {
                        "port": 80,
                        "dnsName": "ui-tls"
                    }
                ],
                "tls": {
                  "issuerCertificateAuthority": {
                    "awsPcaAuthorityArn": "'${CERTIFICATE_AUTH_ARN}'"
                  },
                  "roleArn": "'${ECS_SERVICE_CONNECT_ROLE_ARN}'"
                }
            }
        ]
    }'
```

Chờ việc triển khai service hoàn tất (khoảng 2 phút):

```bash
echo "Waiting for service to stabilize..."
aws ecs wait services-stable --cluster retail-store-ecs-cluster --services ui-tls
```

#### Truy cập ứng dụng web qua HTTPS

Lấy DNS name của Application Load Balancer:

```bash
export RETAIL_ALB=$(aws elbv2 describe-load-balancers --name retail-store-ecs-ui \
 --query 'LoadBalancers[0].DNSName' --output text)

echo https://${RETAIL_ALB} ; echo
```

Do workshop này sử dụng chứng chỉ tự ký, trình duyệt của bạn sẽ hiển thị cảnh báo bảo mật. Để tiếp tục:

1. Chọn nút **Advanced** trong trình duyệt của bạn
2. Nhấp vào `Proceed to retail-store-ecs-ui-XXXXXXXXXXX.us-west-2.elb.amazonaws.com (unsafe)`

![Invalid Certificate Advanced](/images/4-service-connect-with-tls/2-deploy-ui-service-with-tls/image.png)
*Hình 1. Invalid Certificate Advanced*

Khi truy cập thành công, bạn sẽ thấy trang chủ của ứng dụng:

![App Home page with TLS enabled](/images/4-service-connect-with-tls/2-deploy-ui-service-with-tls/image-1.png)
*Hình 2. Trang chủ ứng dụng với TLS được bật*