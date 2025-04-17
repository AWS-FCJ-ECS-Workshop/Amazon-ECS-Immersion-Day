+++
title = "Introduction"
date = 2020-05-14T00:38:32+07:00
weight = 1
chapter = false
pre = "<b>1. </b>"
+++

{{% notice info %}}
**Prerequisites:** Complete the [Fundamentals](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/fundamentals/) chapter before starting this lab.
{{% /notice %}}

### What Is an ECS Rolling Deployment?

Rolling deployment is a deployment strategy that gradually replaces old versions of your application with new ones, minimizing downtime and ensuring continuous availability of the service.

When using the rolling update deployment type in Amazon ECS, the platform replaces running tasks with new ones based on your defined service configuration. This behavior is controlled by two key parameters:

- **minimumHealthyPercent**: The minimum number of tasks that must remain in the RUNNING state during the deployment.

- **maximumPercent**: The maximum number of tasks (in either RUNNING or PENDING state) allowed during the deployment.

#### Example

If your desired task count is 4:

- With minimumHealthyPercent = 50, ECS can stop up to 2 tasks at once before starting new ones.

- With maximumPercent = 200, ECS can start up to 4 new tasks before stopping old ones.

{{% notice note %}}
The speed of deployment depends on the values of minimumHealthyPercent and maximumPercent. If your ECS service is integrated with an Application Load Balancer (ALB), health check settings also affect deployment timing.
For tips on optimizing deployment speed, refer to: [this post on speeding up Amazon ECS container deployments](https://nathanpeck.com/speeding-up-amazon-ecs-container-deployments/).
{{% /notice %}}

### ECS Rolling Deployment with AWS CodePipeline

You can automate rolling deployments to ECS using AWS CodePipeline. Below is a step-by-step guide to set it up:

1. **Define Your ECS Task and Service**

   Start by defining your ECS **task definition** and **service**:

   - Specify the container image, resource limits, networking, and environment settings.

   - Configure service-level options such as desired task count and load balancing.

2. **Set Up Your AWS CodePipeline**

   Create a pipeline with the following stages:

   - Source

   - Build

   - Deploy

3. **Source Stage**

   Configure CodePipeline to connect to your source code repository (e.g., **GitHub**, **AWS CodeCommit**).

   Each time a code change is detected, the pipeline will automatically trigger a new deployment process.

4. **Build Stage**

   Use **AWS CodeBuild** (or another build tool) to:

   - Compile your code

   - Run automated tests (optional)

   - Build and tag your Docker image

   - Push the image to **Amazon ECR** (Elastic Container Registry)

5. **Deploy Stage**

   Use **AWS CodeDeploy** to manage the rolling deployment:

   - Link CodeDeploy to your ECS service

   - Configure a deployment group with rolling update settings (e.g., minimumHealthyPercent, maximumPercent)

   - CodeDeploy will handle task replacements and ensure traffic is routed correctly through the load balancer

6. **Testing & Approval (Optional)**

   Add extra stages to enhance control and quality:

   - Run integration or smoke tests after deployment

   - Add **manual approval** steps to require a human checkpoint before the deploy proceeds

7. **Monitoring & Rollback**

   Enable monitoring and alerting for real-time visibility:

   - Use **CloudWatch Logs**, **ECS Service Events**, and **CloudWatch Alarms**

   - If an issue is detected, CodeDeploy can **automatically rollback** to the previous working version

8. **Continuous Improvement**

   After each deployment:

   - Review metrics, logs, and feedback

   - Identify bottlenecks or failures

   - Iterate and improve your deployment strategy

By following this structured approach, you can build a reliable, automated rolling deployment pipeline using AWS CodePipeline and Amazon ECS. This setup allows you to deploy updates safely, reduce risk, and maintain high availability with minimal effort.

![Deployment-Rolling](/images/1/Deployment-Rolling.png?width=90pc)
