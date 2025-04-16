---
title: "Test the DynamoDB Integration"
date: "`r Sys.Date()`"
weight: 4
chapter: false
pre: "<b> 3.4. </b>"
---

In this section, we will test that the new `Carts` service is able to store the user's shopping items in the [Amazon DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html)  table.

Run the following command to review the content of the DynamoDB table:

If you have not placed any items in the shopping cart after deploying the `Carts` service, the table should be empty.

```bash
    aws dynamodb scan --table-name retail-store-ecs-carts
```

The output shows that the table is currently empty:

```json
    {
        "Items": [],
        "Count": 0,
        "ScannedCount": 0,
        "ConsumedCapacity": null
    }
```

Now, navigate to the retail store application and add a few items to the user's shopping cart by clicking on the _Add to Cart_ button.

Use the following command to get the application URL:

```bash
    export RETAIL_ALB=$(aws elbv2 describe-load-balancers --name retail-store-ecs-ui \
        --query 'LoadBalancers[0].DNSName' --output text)
    
    echo http://${RETAIL_ALB} ; echo
```

![Add item to Cart](/images/3-iam-roles/3.4-test-dynamodb-integration/image.png)
*Figure 1. Adding item to Cart*

After adding items to the cart, run another table scan to review the updated content of the DynamoDB table:

```bash
    aws dynamodb scan --table-name retail-store-ecs-carts
```

The result of the table scan now shows the items stored in the DynamoDB table:

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

Congratulations! You have successfully completed the test of the DynamoDB integration with the `Carts` service.