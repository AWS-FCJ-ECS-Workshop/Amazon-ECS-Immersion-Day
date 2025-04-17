+++
title = "Execute Rolling Deployment"
date = 2024-05-14T00:38:32+07:00
weight = 4
chapter = false
pre = "<b>4. </b>"
+++

### Create Code Pipeline

1. Select **Pipeline** under the Developer Tool tab
2. Select **Create pipeline**
   ![CodePipeline-2](/images/4/4-2.png?width=90pc)

3. In the **Choose creation option** section

   - Select **Build custom pipeline**
   - Select **Next**

   ![CodePipeline-3](/images/4/4-3.png?width=90pc)

4. In the **Choose pipeline settings** section

   - Pipeline name: Enter your pipeline name
   - Execution mode: **Superseded**
   - Service role: **New service role**
   - Role name: Enter your role name
     (e.g., `AWSCodePipelineServiceRole-ECS-CodePipeline-Rolling-Deployment`)
   - Select **Next**

   ![CodePipeline-4](/images/4/4-4.png?width=90pc)

5. In the **Add source stage** section

   - Source provider: **Github**
   - Connection: Select the created connection
   - Repository name: Select your repo
   - Default branch: your branch (e.g., `main`)

   ![CodePipeline-5](/images/4/4-5.png?width=90pc)

   - Leave the rest as default, select **Next**

   ![CodePipeline-6](/images/4/4-6.png?width=90pc)

6. In the **Add build stage** section

   - Build provider: **Other build provider**
   - Select **AWS CodeBuild**
   - Project name: Select your CodeBuild project

   ![CodePipeline-7](/images/4/4-7.png?width=90pc)

   - Build type: **Single build**
   - Region: Select your Region
   - Select **Next**

   ![CodePipeline-8](/images/4/4-8.png?width=90pc)

7. In the **Add test stage** section

   - Select **Skip test stage**

   ![CodePipeline-9](/images/4/4-9.png?width=90pc)

8. In the **Add deploy stage** section

   - Deploy provider: **Amazon ECS**
   - Region: Your Region
   - Cluster name: Select the created cluster: `retail-store-ecs-cluster`
   - Service name: **ui**
   - Image definitions file: `imagedefinitions.json`
   - Select **Next**

   ![CodePipeline-10](/images/4/4-10.png?width=90pc)
   ![CodePipeline-11](/images/4/4-11.png?width=90pc)

9. Review the Pipeline configuration

   - Select **Create pipeline**

   ![CodePipeline-12](/images/4/4-12.png?width=90pc)

10. Wait until the processes from **Source > Build > Deploy** are completed
    ![CodePipeline-13](/images/4/4-13.png?width=90pc)
