---
title : "Clean Resources"
date :  "`r Sys.Date()`" 
weight : 5
chapter : false
pre: "<b> 5. </b>"
---

{{% notice info %}}
Important: This Clean Up Resources section will only delete the services created within this section.
To remove all resources, you must delete them sequentially starting from the [Fundamentals](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/fundamentals/) section.
{{% /notice %}}

To ensure you don't incur unnecessary charges, follow these steps to clean up the resources created during this workshop:

1. Delete the Cart Security Group
   * Navigate to the EC2 Console > Security Groups
   * Select the security group named `ecs-lab-cart-sg`
   * Choose **Delete security group** from the Actions menu
   * Confirm deletion

![Delete Security Group](/images/5-clean-resources/image.png)

2. Delete DynamoDB Table
   * Go to the DynamoDB Console
   * Select the table created for this workshop
   * Choose **Delete table**
   * Type "delete" to confirm
   * Wait for the table to be completely removed

![Delete DynamoDB Table](/images/5-clean-resources/image-1.png)

3. Remove Cart Services
   * Access the ECS Console
   * Select the cart service cluster
   * Choose the cart service
   * Select **Delete**
   * Wait for the service and tasks to be terminated

![Delete Cart Services](/images/5-clean-resources/image-2.png)

4. Disable Amazon GuardDuty
   * Navigate to the GuardDuty Console
   * Choose **Settings**
   * Select **Disable GuardDuty**
   * Confirm the action
   * Wait for GuardDuty to be fully disabled

![Disable GuardDuty](/images/5-clean-resources/image-3.png)

**Important**: Verify all resources are properly deleted to avoid ongoing charges. Check your AWS Console to ensure no related resources remain active.