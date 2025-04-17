---
title: "Test the DynamoDB Integration"
date: "`r Sys.Date()`"
weight: 4
chapter: false
pre: "<b> 3.4. </b>"
---

This section demonstrates how to verify that the `Carts` service correctly stores shopping items in [Amazon DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html).

First, check the current content of the DynamoDB table. If you haven't added any items to the shopping cart after deploying the `Carts` service, the table should be empty:

```bash
aws dynamodb scan --table-name retail-store-ecs-carts
```

Expected output for an empty table:

```json
{
    "Items": [],
    "Count": 0,
    "ScannedCount": 0,
    "ConsumedCapacity": null
}
```

Next, access the retail store application to add items to your shopping cart. Retrieve the application URL using:

```bash
export RETAIL_ALB=$(aws elbv2 describe-load-balancers --name retail-store-ecs-ui \
    --query 'LoadBalancers[0].DNSName' --output text)

echo http://${RETAIL_ALB} ; echo
```

![Add item to Cart](/images/3-iam-roles/3.4-test-dynamodb-integration/image.png)
*Figure 1. Adding item to Cart*

After adding items to your cart, verify that they are stored in DynamoDB by running another table scan:

```bash
aws dynamodb scan --table-name retail-store-ecs-carts
```

The scan should now show the items stored in the DynamoDB table:

```json
{
    "Items": [
        {
            "quantity": {
                "N": "1"
            },
            "unitPrice": {
                "N": "5100"
            },
            "itemId": {
                "S": "808a2de1-1aaa-4c25-a9b9-6612e8f29a38"
            },
            "id": {
                "S": "2b81546e-5dc8-47e6-be81-eca8dd704af9:808a2de1-1aaa-4c25-a9b9-6612e8f29a38"
            },
            "customerId": {
                "S": "2b81546e-5dc8-47e6-be81-eca8dd704af9"
            }
        },
        ...
    ],
    "Count": 3,
    "ScannedCount": 3,
    "ConsumedCapacity": null
}
```

You have now verified that the DynamoDB integration with the `Carts` service is working correctly.