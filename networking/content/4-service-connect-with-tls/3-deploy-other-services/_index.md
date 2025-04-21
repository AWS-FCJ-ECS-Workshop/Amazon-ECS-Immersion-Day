---
title: "Deploy Other Services with TLS"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: "<b> 4.3 </b>"
---

In the previous section, we configured ECS Service Connect on the UI Service to encrypt communication between the Application Load Balancer and the UI Service. In this section, we will set up ECS Service Connect for the communication between **UI-TLS**, **Catalog**, and **Asset** services.

---

### Set up TLS on ECS Service Connect for the Catalog Service

Update the **Catalog** Service to enable TLS for ECS Service Connect:

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

---

### Set up TLS on ECS Service Connect for the Assets Service

Update the **Assets** Service to enable TLS for ECS Service Connect:

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

Now, all traffic between the **UI-TLS**, **Catalog**, and **Assets** services is encrypted.