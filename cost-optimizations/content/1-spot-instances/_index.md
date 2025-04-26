---
title: "Spot intances"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: "<b> 1. </b>"
---

{{% notice info %}}
You must have completed the following chapters as prerequisites for this lab: [Fundamentals](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/fundamentals/)
{{% /notice %}}

With Fargate Spot, you can run interruption-tolerant Amazon ECS tasks at a [70% discounted rate](https://aws.amazon.com/fargate/pricing/) compared to the Fargate on-demand price. Fargate Spot runs tasks on spare compute capacity. When AWS needs the capacity back, your tasks are interrupted with a two-minute warning.

### Capacity Providers

Amazon ECS capacity providers manage the scaling of infrastructure for tasks in your clusters. Each cluster can have one or more capacity providers and an optional capacity provider strategy. The capacity provider strategy determines how tasks are distributed across the cluster's capacity providers. When you run a standalone task or create a service, you either use the cluster's default capacity provider strategy or a capacity provider strategy that overrides the default one.

### Modify Cluster to Enable Spot Capacity Provider

By default, Fargate uses the Fargate capacity provider, which relies on on-demand instances:

```bash
aws ecs describe-clusters --clusters retail-store-ecs-cluster
```

```json
{
    "clusters": [
        {
            "clusterArn": "arn:aws:ecs:us-west-2:111111111111:cluster/retail-store-ecs-cluster",
            "clusterName": "retail-store-ecs-cluster",
            "status": "ACTIVE",
            "registeredContainerInstancesCount": 0,
            "runningTasksCount": 2,
            "pendingTasksCount": 0,
            "activeServicesCount": 1,
            "statistics": [],
            "tags": [],
            "settings": [],
            "capacityProviders": [],
            "defaultCapacityProviderStrategy": []
        }
    ],
    "failures": []
}
```

We will modify the current cluster to add the `FARGATE_SPOT` capacity provider and define our strategy:

```bash
aws ecs put-cluster-capacity-providers \
    --cluster retail-store-ecs-cluster \
    --capacity-providers FARGATE FARGATE_SPOT \
    --default-capacity-provider-strategy \
        capacityProvider=FARGATE,weight=1,base=1 \
        capacityProvider=FARGATE_SPOT,weight=4
```

```json
{
    "cluster": {
        "clusterArn": "arn:aws:ecs:us-west-2:111111111111:cluster/retail-store-ecs-cluster",
        "clusterName": "retail-store-ecs-cluster",
        "status": "ACTIVE",
        "registeredContainerInstancesCount": 0,
        "runningTasksCount": 0,
        "pendingTasksCount": 0,
        "activeServicesCount": 0,
        "statistics": [],
        "tags": [],
        "settings": [
            {
                "name": "containerInsights",
                "value": "enabled"
            }
        ],
        "capacityProviders": [
            "FARGATE",
            "FARGATE_SPOT"
        ],
        "defaultCapacityProviderStrategy": [
            {
                "capacityProvider": "FARGATE",
                "weight": 1,
                "base": 0
            },
            {
                "capacityProvider": "FARGATE_SPOT",
                "weight": 4,
                "base": 1
            }
        ],
        "attachments": [],
        "attachmentsStatus": "UPDATE_IN_PROGRESS"
    }
}
```

The command above with the `--default-capacity-provider-strategy` flag means that if no capacity provider or launch type is specified during the creation of a service or running a task, this default capacity provider strategy for the ECS cluster will be used.

- Regular `FARGATE` gets a base of 0 to ensure that tasks do not use on-demand instances.
- `FARGATE_SPOT` gets a higher weight (4:1), meaning more tasks will prefer Spot when possible.
- The `base` value designates the minimum number of tasks to run on the specified capacity provider. Only one capacity provider in a capacity provider strategy can have a base defined.
- The `weight` value designates the relative percentage of the total number of launched tasks that should use the specified capacity provider. For example, if you have a strategy that contains two capacity providers, and both have a weight of 1, then when the base is satisfied, the tasks will be split evenly across the two capacity providers.

### Modify Retail-Store Application

The service is not yet using Spot. There will be no specific capacity provider in the service, so the `FARGATE` default will be used.

Let's modify the current `ui` service to leverage Spot instances and force a new deployment:

```bash
aws ecs update-service \
    --cluster retail-store-ecs-cluster \
    --service ui \
    --capacity-provider-strategy \
        capacityProvider=FARGATE,weight=1,base=0 \
        capacityProvider=FARGATE_SPOT,weight=4,base=1 \
    --force-new-deployment
```

```json
{
    "service": {
        "serviceArn": "arn:aws:ecs:us-west-2:111111111111:service/retail-store-ecs-cluster/ui",
        "serviceName": "ui",
        "clusterArn": "arn:aws:ecs:us-west-2:111111111111:cluster/retail-store-ecs-cluster",
        "loadBalancers": [],
        "serviceRegistries": [],
        "status": "ACTIVE",
        "desiredCount": 1,
        "runningCount": 1,
        "pendingCount": 0,
        "capacityProviderStrategy": [
            {
                "capacityProvider": "FARGATE",
                "weight": 1,
                "base": 0
            },
            {
                "capacityProvider": "FARGATE_SPOT",
                "weight": 4,
                "base": 1
            }
        ],
        "platformVersion": "LATEST",
        "platformFamily": "Linux",
        "[...]": "..."
    }
}
```

Note that when switching from launch type to capacity provider strategy on an existing service, or making a change to a capacity provider strategy on a service that is already using one, you must force a new deployment.

After the service deployment finishes, you can verify the change using this command to get the capacity provider name of `ui` tasks:

```bash
tasks_arns=$(aws ecs list-tasks --cluster retail-store-ecs-cluster --service-name ui --query 'taskArns' --output text)
echo ${tasks_arns} | xargs aws ecs describe-tasks --cluster retail-store-ecs-cluster \
    --tasks \
    --query 'tasks[].{lastStatus: lastStatus, startedAt: startedAt, status: healthStatus, capacityProvider: capacityProviderName}' \
    --output table
```

You should see `FARGATE_SPOT` in the `capacityProvider` column:

```plaintext
+--------------------+-----------+---------------------------------------+-----------+
| capacityProvider   | lastStatus|            startedAt                  |  status   |
+--------------------+-----------+---------------------------------------+-----------+
| FARGATE_SPOT       | RUNNING   | 1970-01-01T00:01:44.181000+01:00      | HEALTHY   |
| FARGATE_SPOT       | RUNNING   | 1970-01-01T00:00:04.624000+01:00      | HEALTHY   |
+--------------------+-----------+---------------------------------------+-----------+
```

### How to Handle Termination

Typically, your container will receive the signal [`SIGTERM`](https://en.wikipedia.org/wiki/Signal_(IPC)#SIGTERM) (15, software termination signal). Most frameworks, applications, and web servers receive this signal and gracefully shut down.

If the container is not stopped before the `stopTimeout` period, ECS will send the signal [`SIGKILL`](https://en.wikipedia.org/wiki/Signal_(IPC)#SIGKILL) (9, non-catchable, non-ignorable kill) and may result in data loss or corruption. Note that the `stopTimeout` value cannot be longer than [120 seconds](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_definition_parameters.html#container_definition_timeout).

To verify, you can stop one of your tasks in a non-production environment and verify it stops before the `stopTimeout` value and check the time difference between the stop initiation and when the container was actually terminated in the logs:

```bash
task_arn=$(aws ecs list-tasks --cluster retail-store-ecs-cluster --service-name ui --query 'taskArns[0]' --output text) # get only the first task
aws ecs stop-task --cluster retail-store-ecs-cluster --task ${task_arn} --reason "Test from ecs-immersion-day"
```

As an example in our application (Spring Boot), it will print the following messages indicating `SIGTERM` has been received (when the `LOGGING_LEVEL_ROOT` environment variable is set to `DEBUG`):

```plaintext
[...]
1970-01-01T00:00:27.762Z DEBUG 7 --- [ionShutdownHook] o.s.b.a.ApplicationAvailabilityBean : Application availability state ReadinessState changed from ACCEPTING_TRAFFIC to REFUSING_TRAFFIC
1970-01-01T00:00:27.778Z DEBUG 7 --- [ionShutdownHook] o.s.c.s.DefaultLifecycleProcessor : Bean 'webServerGracefulShutdown' completed its stop procedure
[...]
```

Fargate Spot termination notices are sent to Amazon EventBridge. For more information, you can read [the documentation](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/fargate-capacity-providers.html#fargate-capacity-providers-termination).