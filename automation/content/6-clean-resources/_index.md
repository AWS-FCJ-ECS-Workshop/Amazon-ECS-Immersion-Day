+++
title = "Clean Resource"
date = 2024-05-14T00:38:32+07:00
weight = 6
chapter = false
pre = "<b>6. </b>"
+++

We will proceed with the following steps to delete the resources we created in this exercise.

### Delete CodePipeline

In the Pipelines section:

- Select the created Pipeline
- Select **Delete pipeline**
  ![Delete-Pipeline-1](/images/6/6-1.png?width=90pc)

- Enter `delete` to confirm deletion
- Select **Delete**
  ![Delete-Pipeline-2](/images/6/6-2.png?width=90pc)

### Delete CodeBuild

In the Build projects section:

- Select the created project
- Select **Actions**
- Select **Delete**
  ![Delete-Pipeline-3](/images/6/6-3.png?width=90pc)

- Enter `delete` to conform delection
- Select **Delete**
  ![Delete-Pipeline-4](/images/6/6-4.png?width=90pc)

### Delete S3

In the S3 Bucket section:

- Select the created bucket
- Select **Empty**
  ![Delete-S3](/images/6/6-5.png?width=90pc)
- Enter `permanently delete` to confirm deletion
  ![Delete-S3](/images/6/6-6.png?width=90pc)
- Continue to select the created bucket
- Select **Delete**
  ![Delete-S3](/images/6/6-7.png?width=90pc)
- Enter the name of the bucket to confirm deletion
  ![Delete-S3](/images/6/6-8.png?width=90pc)

{{% notice info %}}
The remaining resources will be used for the next lab.
{{% /notice %}}
