+++
title = "Conclusion"
date = 2024-08-19T00:38:32+07:00
weight = 7
chapter = false
pre = "<b>7. </b>"
+++

In this Automation module, we successfully implemented several key components that enable the automated deployment of containerized applications to Amazon ECS using AWS CodePipeline, including:

1. **Integration of AWS CodePipeline with ECS:** We automated the entire workflow from code changes to deployment, ensuring faster and more reliable software releases.

2. **Four Main Pipeline Stages:**

   1. Source Stage: Pulls the latest code from a source repository like GitHub or AWS CodeCommit.

   2. Build Stage: Compiles the code, builds a Docker image using AWS CodeBuild, and optionally runs tests.

   3. Registry Push: Pushes the built image to Amazon ECR, with optional security scanning.

   4. Deploy Stage: Updates the ECS service using the new Docker image by modifying the task definition and launching updated tasks.

3. **Rolling Deployment Strategy:**

   - We implemented a rolling update strategy to gradually replace old versions with new ones, minimizing downtime and maintaining service availability.

   - Controlled through key parameters such as minimumHealthyPercent and maximumPercent to manage speed and stability.

4. **Optional Testing & Approval Steps:**

   - Added integration or smoke tests post-deployment.

   - Included manual approval stages for enhanced control before proceeding with deployment.

5. **Monitoring & Rollback:**

   - Enabled monitoring with CloudWatch Logs, ECS Service Events, and CloudWatch Alarms.

   - Configured automatic rollback to the last stable version if deployment fails.

![Conclusion](/images/7/ECS-Pipeline-7-1.png?width=90pc)

#### Next Steps

Continue your Amazon ECS learning journey by exploring these specialized workshop modules:

1. [Storage](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/storage) - Integrate Amazon EFS for persistent storage

Each module provides detailed hands-on experience to help you build production-ready ECS applications.

#### Credits

Le Minh Nghia - https://www.linkedin.com/in/minhnghia2k3/

Tran Doan Cong Ly - https://www.linkedin.com/in/trandoancongly/

#### References

Automation - https://catalog.workshops.aws/ecs-immersion-day/en-US/80-ci-cd

Rolling Deployent - https://catalog.workshops.aws/ecs-immersion-day/en-US/80-ci-cd/rolling
