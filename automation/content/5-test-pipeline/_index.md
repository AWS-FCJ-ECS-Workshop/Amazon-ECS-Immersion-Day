+++
title = "Test Pipeline"
date = 2024-05-14T00:38:32+07:00
weight = 5
chapter = false
pre = "<b>5. </b>"
+++

### Check the web application

Paste the URL into a web browser to access the application and check the web application's title and colors, which we are going to change.

Get load balancer URL:

```
export RETAIL_ALB=$(aws elbv2 describe-load-balancers --name retail-store-ecs-ui \
 --query 'LoadBalancers[0].DNSName' --output text)

echo http://${RETAIL_ALB}
```

![Test-pipeline-1](/images/5/5-1.png?width=90pc)
![Test-pipeline-2](/images/5/5-2.png?width=90pc)

### Deploy changes

Now, let's run the created CI/CD pipeline to perform a rolling deployment at VS Code.

![Test-pipeline-3](/images/5/5-3-fix.png?width=90pc)

- Rename the main UI banner with the title: **Retail Store Sample CodePipeline**
- Change the color of the navigation bar to green (#0AD24A)

```
FILE="$HOME/Downloads/retail-store-sample-app-0.7.0/src/ui/src/main/resources/templates/fragments/layout.html"
```

{{% notice info %}}
Replace the path named "**/Downloads/**" with the name of your source code storage directory. If you encounter any errors, you can edit the instructed content directly in your source code.
{{% /notice %}}

```
sed -i 's|Retail Store Sample</a>|Retail Store Sample CodePipeline</a>|g' $FILE

sed -i 's|<nav class="navbar navbar-expand-lg navbar-light bg-light">|<nav class="navbar navbar-expand-lg navbar-light bg-light" style="background-color:#0AD24A !important">|g' $FILE
```

![Test-pipeline-4](/images/5/5-4-fix.png?width=90pc)

Verify if the changes have been successfully applied by running the following command:

```
cat $FILE | grep "style=\"background-color:#0AD24A"
cat $FILE | grep CodePipeline
```

![Test-pipeline-5](/images/5/5-10.png?width=90pc)

Commit the changes to the Github repository:

```
git init
git status
git add .
git commit -m "update UI and color banner"
git push
```

{{% notice info %}}
It may take a few seconds for the pipeline to start.
{{% /notice %}}
![Test-pipeline-6](/images/5/5-11.png?width=90pc)

Navigate to the AWS CodePipeline console and click on the pipeline to open the latest pipeline execution.
![Test-pipeline-7](/images/5/5-6.png?width=90pc)
![Test-pipeline-8](/images/5/5-7.png?width=90pc)
![Test-pipeline-9](/images/5/5-8.png?width=90pc)

When the Deploy stage begins, click on the following link to review the deployment process: [**Open ECS Deployment dashboard**](https://console.aws.amazon.com/ecs/v2/clusters/retail-store-ecs-cluster/services/retail-store-ui-cicd/deployments)

### Check web application's change

Once the deployment is completed, access the web application:

```
export RETAIL_ALB=$(aws elbv2 describe-load-balancers --name retail-store-ecs-ui \
 --query 'LoadBalancers[0].DNSName' --output text)

echo http://${RETAIL_ALB}:8080 ; echo
```

Confirm that the title has changed to Retail Store Sample CodePipeline and the navigation bar now has a green (#0AD24A) background.

#### - After

![Test-pipeline-12](/images/5/5-12.png?width=90pc)

#### - Before

![Test-pipeline-13](/images/5/5-13.png?width=90pc)

Congratulations! Through this lab, you have successfully built a CI/CD pipeline and deployed a new ECS service using a rolling deployment strategy.
