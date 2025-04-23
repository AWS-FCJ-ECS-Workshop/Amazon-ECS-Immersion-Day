---
title: "Deploy Other Services with TLS"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: "<b> 4.3 </b>"
---

After configuring TLS encryption between the Application Load Balancer and UI Service using ECS Service Connect, we will now implement TLS encryption for communication between the **UI-TLS**, **Catalog**, and **Asset** services.

#### Configure TLS for the Catalog Service

Execute the following command to enable TLS encryption for the Catalog Service using ECS Service Connect:

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

#### Configure TLS for the Assets Service

Execute the following command to enable TLS encryption for the Assets Service using ECS Service Connect:

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

Upon completion of these configurations, all communication between the **UI-TLS**, **Catalog**, and **Assets** services will be encrypted using TLS, ensuring secure data transmission within your application.