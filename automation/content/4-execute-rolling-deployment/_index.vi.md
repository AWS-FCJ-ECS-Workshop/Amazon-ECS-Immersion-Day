+++
title = "Tiến hành triển khai Rolling"
date = 2024-05-14T00:38:32+07:00
weight = 4
chapter = false
pre = "<b>4. </b>"
+++

### Create Code Pipeline

1. Chọn **Pipeline** "dưới tab Developer Tool
2. Chọn **Create pipeline**
   ![CodePipeline-2](/images/4/4-2.png?width=90pc)

3. Trong phần **Choose creation option**

   - Chọn **Build custom pipeline**
   - Chọn **Next**

   ![CodePipeline-3](/images/4/4-3.png?width=90pc)

4. Trong phần **Choose pipeline settings**

   - Pipeline name: Nhập pipeline name của bạn
   - Execution mode: **Superseded**
   - Service role: **New service role**
   - Role name: Nhập role name của bạn
     (e.g., `AWSCodePipelineServiceRole-ECS-CodePipeline-Rolling-Deployment`)
   - Chọn **Next**

   ![CodePipeline-4](/images/4/4-4.png?width=90pc)

5. Trong phần **Add source stage**

   - Source provider: **Github**
   - Connection: Chọn connection đã tạo
   - Repository name: Chọn repo của bạn
   - Default branch: Nhánh (Branch) của bạn (e.g., `main`)

   ![CodePipeline-5](/images/4/4-5.png?width=90pc)

   - Giữ nguyên các phần còn lại theo mặc định, chọn **Next**

   ![CodePipeline-6](/images/4/4-6.png?width=90pc)

6. Trong phần **Add build stage**

   - Build provider: **Other build provider**
   - Chọn **AWS CodeBuild**
   - Project name: Chọn CodeBuild project của bạn

   ![CodePipeline-7](/images/4/4-7.png?width=90pc)

   - Build type: **Single build**
   - Region: Chọn Region của bạn
   - Chọn **Next**

   ![CodePipeline-8](/images/4/4-8.png?width=90pc)

7. Trong phần **Add test stage**

   - Chọn **Skip test stage**

   ![CodePipeline-9](/images/4/4-9.png?width=90pc)

8. Trong phần **Add deploy stage**

   - Deploy provider: **Amazon ECS**
   - Region: Region của bạn
   - Cluster name: Chọn cluster đã tạo: `retail-store-ecs-cluster`
   - Service name: **ui**
   - Image definitions file: `imagedefinitions.json`
   - Chọn **Next**

   ![CodePipeline-10](/images/4/4-10.png?width=90pc)
   ![CodePipeline-11](/images/4/4-11.png?width=90pc)

9. Xem lại cấu hình Pipeline

   - Chọn **Create pipeline**

   ![CodePipeline-12](/images/4/4-12.png?width=90pc)

10. Chờ cho đến khi các tiến trình từ **Source > Build > Deploy** hoàn tất
    ![CodePipeline-13](/images/4/4-13.png?width=90pc)
