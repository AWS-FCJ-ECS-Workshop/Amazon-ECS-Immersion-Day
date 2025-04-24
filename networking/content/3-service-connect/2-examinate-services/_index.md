---
title: "Examining the services"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: "<b> 3.2. </b>"
---

Navigate to the Amazon ECS console to inspect the deployed services.

[Open Amazon ECS console](https://console.aws.amazon.com/ecs/v2/clusters/retail-store-ecs-cluster/services)

The cluster contains three running services:

![Cluster Service List](/images/3-service-connect/2-examinate-services/image.png)
*Figure 1. Cluster Service List*

On the **Tasks** tab, you can view all running tasks:

![Cluster Task List](/images/3-service-connect/2-examinate-services/image-1.png)
*Figure 2. Cluster Task List*

Select any running task and navigate to the **Containers** section. Each task contains three containers:
- The `application` container
- The `ecs-service-connect` proxy container for service discovery and connectivity
- The `aws-guardduty-agent` container for runtime security monitoring

For more details about Service Connect proxy, see the [Service Connect concepts documentation](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/service-connect-concepts.html#service-connect-concepts-proxy).

![Cluster Task containers](/images/3-service-connect/2-examinate-services/image-2.png)
*Figure 3. Cluster Task Containers*

#### The Namespace

Service Connect leverages [AWS Cloud Map](https://aws.amazon.com/cloud-map/) namespaces to logically group Amazon ECS tasks that need to communicate. Key points about namespaces:

- Each Amazon ECS service can belong to only one namespace
- Services within a namespace can span multiple Amazon ECS clusters in the same AWS Region and account

To view the Cluster Namespaces:
1. Open the Amazon ECS dashboard
2. Select **Namespace** from the navigation menu

![Cluster Namespace](/images/3-service-connect/2-examinate-services/image-3.png)
*Figure 4. Cluster Namespace*

Select a namespace to view its AWS Cloud Map configuration details. The **Discovery Name** field shows the short name used for service connectivity within the same namespace (for example, `http://assets`).

![Cluster Namespace](/images/3-service-connect/2-examinate-services/image-4.png)
*Figure 5. Services in **retailstore.local** namespace*