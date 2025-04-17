---
title: "Creating DynamoDB"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: "<b> 2.2. </b>"
---

Create a DynamoDB table to store shopping cart information for the retail store application.

{{% notice info %}}
The table and index names must match exactly as specified, as they will be referenced in subsequent AWS CLI commands.
{{% /notice %}}

#### Creating the DynamoDB Table

1. Access the AWS Management Console and navigate to the **DynamoDB** service

2. Select **Create table** and enter these configuration details:
   * Table name: `retail-store-ecs-carts`
   * Partition key: `id` (String)
   * Keep all other settings at their default values

![alt text](/images/2-prerequisites/2.2-create-dynamodb/image.png)
*Figure 2: DynamoDB table configuration*

3. Wait for the table creation to complete. The table status will display as "Active" when ready

![alt text](/images/2-prerequisites/2.2-create-dynamodb/image-1.png)
*Figure 3: DynamoDB table successfully created*

#### Adding a Global Secondary Index

1. On the `retail-store-ecs-carts` table details page, navigate to the **Indexes** tab and select **Create index**

2. Set up the index with the following parameters:
   * Partition key: `customerId` (String)
   * Index name: `idx_global_customerId`

![alt text](/images/2-prerequisites/2.2-create-dynamodb/image-2.png)
*Figure 4: Global secondary index configuration*