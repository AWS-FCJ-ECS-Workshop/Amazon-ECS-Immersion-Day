---
title: "Clean resources"
date: "`r Sys.Date()`"
weight: 6
chapter: false
pre: "<b> 6. </b>"
---

### Delete ECS Application auto scaling

1. Navigate to the ECS cluster containing your service

2. Select the service you want to modify

3. Choose the **Update Service** button

4. In the Service Auto Scaling section:
   - Set **Minimum number of tasks** to 1
   - Set **Desired number of tasks** to 1 
   - Set **Maximum number of tasks** to 1

5. Remove any existing scaling policies:
   - Go to **Application Auto Scaling** in the AWS Console
   - Select **ECS Services** from the scalable resources
   - Find your service and delete associated scaling policies
   - Delete target tracking policies
   - Delete step scaling policies

6. Verify auto scaling removal:
   - Check service metrics to confirm scaling is disabled
   - Monitor that task count remains fixed at 1
   - Review CloudWatch alarms - delete any associated with removed scaling policies

7. Update service:
   - Choose **Next step** until you reach the final review
   - Select **Update Service** to apply changes
   - Wait for service to stabilize with new configuration

8. Best practices:
   - Document changes in your infrastructure documentation
   - Monitor service performance after removing auto scaling
   - Consider implementing CloudWatch alarms for manual monitoring
   - Keep deployment history for rollback if needed

![alt text](/images/6-clean-resources/image.png)