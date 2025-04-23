---
title: "Triển khai các Service khác với TLS"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: "<b> 4.3 </b>"
---

Sau khi cấu hình mã hóa TLS giữa Application Load Balancer và UI Service sử dụng ECS Service Connect, chúng ta sẽ triển khai mã hóa TLS cho việc giao tiếp giữa các service **UI-TLS**, **Catalog** và **Asset**.

#### Cấu hình TLS cho Catalog Service

Thực thi lệnh sau để kích hoạt mã hóa TLS cho Catalog Service sử dụng ECS Service Connect:

```bash
aws ecs update-service \
  --cluster retail-store-ecs-cluster \
  --service catalog \
  --task-definition retail-store-ecs-catalog \
  --force-new-deployment \
  --service-connect-configuration '{
    "enabled": true,
    "namespace": "retailstore.local",
    "services": [
      {
        "portName": "application",
        "discoveryName": "catalog",
        "clientAliases": [
          {
            "port": 80,
            "dnsName": "catalog"
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

#### Cấu hình TLS cho Assets Service

Thực thi lệnh sau để kích hoạt mã hóa TLS cho Assets Service sử dụng ECS Service Connect:

```bash
aws ecs update-service \
  --cluster retail-store-ecs-cluster \
  --service assets \
  --task-definition retail-store-ecs-assets \
  --force-new-deployment \
  --service-connect-configuration '{
    "enabled": true,
    "namespace": "retailstore.local",
    "services": [
      {
        "portName": "application",
        "discoveryName": "assets",
        "clientAliases": [
          {
            "port": 80,
            "dnsName": "assets"
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

echo "Waiting for service to stabilize..."

aws ecs wait services-stable --cluster retail-store-ecs-cluster --services catalog
aws ecs wait services-stable --cluster retail-store-ecs-cluster --services assets
```

Sau khi hoàn tất các cấu hình này, tất cả giao tiếp giữa các service **UI-TLS**, **Catalog** và **Assets** sẽ được mã hóa bằng TLS, đảm bảo việc truyền dữ liệu an toàn trong ứng dụng của bạn.