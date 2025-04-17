+++
title = "Build stage implemented using AWS CodeBuild"
date = 2020-05-14T00:38:32+07:00
weight = 3
chapter = false
pre = "<b>3. </b>"
+++

### Review the Build Stage of the CI/CD Pipeline

The build stage of the pipeline is powered by AWS CodeBuild, a fully managed build service that runs in the cloud. CodeBuild is responsible for:

- Compiling your source code

- Running unit or integration tests (if configured)

- Producing output artifacts that are ready for deployment

AWS CodeBuild uses a configuration file called **buildspec.yml**, written in YAML format. This file defines build phases, environment variables, and artifact outputs for the build process.

You can refer to the official AWS documentation on buildspec.yml for a detailed breakdown.

### Buildspec Structure Breakdown

Below is a high-level overview of the stages defined in the buildspec.yml used in this workshop:

- install: Installs required tools or dependencies (if needed).

- pre_build:

  - Loads environment variables

  - Logs in to Amazon ECR using the AWS CLI

- build: Builds the Docker container image using the Dockerfile in your repository.

- post_build: Pushes the newly built image to the Amazon ECR repository.

- artifacts: Defines the build output artifacts that will be passed to the next stage of the pipeline.
  In our example, CodeBuild will generate and output the following artifacts:

  - **imagedefinitions.json**: Used to update the ECS service with the new image

  - **imageDetail.json**: Contains metadata about the Docker image built

  - **taskdef.json**: The ECS task definition used for deploying the application
