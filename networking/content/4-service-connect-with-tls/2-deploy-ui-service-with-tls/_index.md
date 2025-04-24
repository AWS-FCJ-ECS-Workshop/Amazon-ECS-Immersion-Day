---
title: "Deploy the UI Service with TLS"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: "<b> 4.2 </b>"
---

This section covers deploying the UI service with TLS-enabled ECS Service Connect and accessing it through HTTPS.

#### Deploy the UI service with TLS enabled in ECS Service Connect

First, retrieve the ECS Service Connect role ARN:

```bash
    export ECS_SERVICE_CONNECT_ROLE_ARN=$(aws iam get-role --role-name ecs-service-connect-certificate-role --query Role.Arn --output text)
```

Create the ECS service with TLS configuration:

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

Monitor the service deployment (approximately 2 minutes):

```bash
    echo "Waiting for service to stabilize..."
    
    aws ecs wait services-stable --cluster retail-store-ecs-cluster --services ui-tls
```

#### Access the web application via HTTPS

Obtain the Application Load Balancer DNS name:

```bash
    export RETAIL_ALB=$(aws elbv2 describe-load-balancers --name retail-store-ecs-ui \
     --query 'LoadBalancers[0].DNSName' --output text)
    
    echo https://${RETAIL_ALB} ; echo
```

Note: This workshop uses a self-signed certificate that browsers will flag as invalid.

To access the application:
1. Click the **Advanced** button in your browser
2. Select `Proceed to retail-store-ecs-ui-XXXXXXXXXXX.us-west-2.elb.amazonaws.com (unsafe)`

![Invalid Certificate Advanced](/images/4-service-connect-with-tls/2-deploy-ui-service-with-tls/image.png)
*Figure 1. Invalid Certificate Advanced*

The application homepage should now display:

![App Home page with TLS enabled](/images/4-service-connect-with-tls/2-deploy-ui-service-with-tls/image-1.png)
*Figure 2. App Home page with TLS enabled*