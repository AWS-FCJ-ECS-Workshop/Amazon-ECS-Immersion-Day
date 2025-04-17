+++
title = "Create S3 Bucket"
date = 2020-05-14T00:38:32+07:00
weight = 2
chapter = false
pre = "<b>2.2 </b>"
+++

### Amazon S3 Bucket – Artifact Store for the Pipeline

As part of setting up our CI/CD workflow, we’ll create an Amazon S3 bucket to act as the artifact store for AWS CodePipeline. This bucket will temporarily hold the build artifacts (such as compiled code, Docker image references, or deployment descriptors) that are passed between different stages of the pipeline.

**Why it's important?**

- CodePipeline uses S3 to store and transfer data between the Source, Build, and Deploy stages.

- It ensures that each stage has access to the outputs it needs to execute properly.

- S3 provides durability, scalability, and reliability, making it ideal for artifact storage.

**Key points:**

- The S3 bucket should be in the same region as your pipeline and ECS service for performance and compliance.

- It’s good practice to enable versioning to preserve historical versions of artifacts.

- You can set up lifecycle policies to automatically clean up old artifacts and save on storage costs.

- Optionally, you can use bucket policies or S3 encryption to enhance security.

This bucket is a foundational component of your pipeline and must be properly configured before building the rest of the CI/CD process.

### Create S3 Bucket

1. Access [S3 Dashboard](https://ap-southeast-1.console.aws.amazon.com/s3)
   ![S3-1](/images/2/2.2-1.png?width=90pc)

2. Select **Create Bucket**
3. Enter **Bucket name**
   ![S3-2](/images/2/2.2-2.png?width=90pc)

4. Leave the rest as default, Select **Create Bucket**
   ![S3-3](/images/2/2.2-3.png?width=90pc)

5. Congratulations, you have successfully created an S3 Bucket.
   ![S3-4](/images/2/2.2-4.png?width=90pc)
