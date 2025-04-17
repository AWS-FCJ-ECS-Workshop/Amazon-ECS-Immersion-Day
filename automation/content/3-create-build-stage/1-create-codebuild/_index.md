+++
title = "Create CodeBuild Project"
date = 2020-05-14T00:38:32+07:00
weight = 3
chapter = false
pre = "<b>3.1 </b>"
+++

1. Access [CodeBuild Dashboard](https://ap-northeast-2.console.aws.amazon.com/codesuite/codebuild/)
   ![CodeBuild-1](/images/3/3.1-1.png?width=90pc)
2. In the **Build project** dashboard, select **Create project**
   ![CodeBuild-2](/images/3/3.1-2.png?width=90pc)
3. In the **Project configuration** section

   Enter **Project name**

   Project type: **Default project**

   ![CodeBuild-3](/images/3/3.1-3.png?width=90pc)

4. In the **Source** section

   - Source provider: Select **Github**

   {{% notice note %}}
   If you have not connected to Github, please follow these steps.
   {{% /notice %}}

   - Select **Manage account credentials**

   ![CodeBuild-4](/images/3/3.1-4.png?width=90pc)

   - Credential type: **Github App**
   - Connection: **Create a new Github connection**

   ![CodeBuild-5](/images/3/3.1-5.png?width=90pc)

   - Enter **Connection name**
   - Select **Connect to Github**

   ![CodeBuild-6](/images/3/3.1-6.png?width=90pc)

   - Enter Username and Password Github
   - Select **Sign in**

   ![CodeBuild-7](/images/3/3.1-7.png?width=90pc)

   - Enter **Authentication code**
   - Select **Verify**

   ![CodeBuild-8](/images/3/3.1-8.png?width=90pc)

   - Select the created **App installation**
   - Select **Connect**

   ![CodeBuild-9](/images/3/3.1-9.png?width=90pc)
   ![CodeBuild-10](/images/3/3.1-10.png?width=90pc)

   - Select the created **Connection**
   - Select **Save**

   ![CodeBuild-11](/images/3/3.1-11.png?width=90pc)

5. After successful connected Github App

   - Source provider: **Github**
   - Repository: **Repository in my Github account**
   - Repository: Select the created Github repo
   - Source version: Branch of the Github repository

   ![CodeBuild-12](/images/3/3.1-12.png?width=90pc)

6. In the **Environment** section

   - Environment image: **Managed image**
   - Running mode: **Container**
   - Operating system: **Ubuntu**
   - Runtime(s): **Standard**
   - Image: **aws/codebuild/standard:5.0**
   - Image version: **Always use the latest image for this runtime version**
   - Service role: **Existing service role**
   - Role ARN: Select role name: `ecs-workshop-codebuild-rolling-deployment`
   - Select **Additional configuration**

   ![CodeBuild-15](/images/3/3.1-15.png?width=90pc)
   ![CodeBuild-16](/images/3/3.1-16.png?width=90pc)
   ![CodeBuild-17](/images/3/3.1-17.png?width=90pc)

   - Privileged: Check **Enable this flag if you want to build Docker images or want your builds to get elevated privileges**

   ![CodeBuild-18](/images/3/3.1-18.png?width=90pc)

7. In the **Buildspec** section

   - Build specifications: **Insert build commands**
   - Select **Switch to editor**

   ![CodeBuild-19](/images/3/3.1-19.png?width=90pc)

   - Add the following JSON:

```
version: 0.2

phases:
install:
commands:
    - echo Build started on `date`
pre_build:
    commands:
      - ACCOUNT_ID=$(aws sts get-caller-identity --query "Account" --output text)
      - echo Logging in to Amazon ECR in $AWS_REGION
      - COMMIT_HASH=$(echo $CODEBUILD_RESOLVED_SOURCE_VERSION | cut -c 1-8)
      - IMAGE_TAG=${COMMIT_HASH:=latest}
      - IMAGE_TAG_I=i$(date +%Y%m%d%H%M%S)-${COMMIT_HASH:=latest}
      - ECR_URI=$ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/retail-store-sample-ui //change the name your ecr
      - echo ECR_URI=$ECR_URI
      - echo IMAGE_TAG=$IMAGE_TAG - echo IMAGE_TAG_I=$IMAGE_TAG_I
      - aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $ECR_URI
build:
    commands:
      - echo Building a container image ...
      - component=ui
      - component_dir="./src/$component"
      - cd $component_dir
      - docker build -t $ECR_URI:$IMAGE_TAG .
      - docker tag $ECR_URI:$IMAGE_TAG $ECR_URI:$IMAGE_TAG_I
      - docker images
post_build:
    commands:
    - docker push $ECR_URI:$IMAGE_TAG_I
    - docker push $ECR_URI:$IMAGE_TAG
    - cd ../..
    - echo Writing image definitions file...
    - printf '[{"name":"application","imageUri":"%s"}]' $ECR_URI:$IMAGE_TAG_I > imagedefinitions.json
    - printf '{"ImageURI":"%s"}' $ECR_URI:$IMAGE_TAG_I > imageDetail.json
    - aws ssm put-parameter --name "/codebuild/retail-store-sample-ui-latest-image" --value "$IMAGE_TAG_I" --type "String" --overwrite
    - echo Build completed on `date`
artifacts:
    name: BuildArtifact
    files:
        - imagedefinitions.json
        - imageDetail.json
        - taskdef.json
```

![CodeBuild-20](/images/3/3.1-20.png?width=90pc)

8. In the **Artifact** section

   - Type: **Amazon S3**
   - Bucket name: Select the created Bucket
   - Name: Enter `ecs-to-s3-artifact`

   ![CodeBuild-21](/images/3/3.1-21.png?width=90pc)

9. Select **Create build project**
   ![CodeBuild-23](/images/3/3.1-23.png?width=90pc)
