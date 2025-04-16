---
title: "Review GuardDuty Setup"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: "<b> 4.1. </b>"
---

By enabling Amazon GuardDuty, you create a detector that represents the GuardDuty service in a specific AWS region, detecting potential security threats in your account. [For more information, click here](https://docs.aws.amazon.com/guardduty/latest/APIReference/API_CreateDetector.html) .

Amazon GuardDuty for runtime monitoring in Amazon ECS is already enabled in the workshop account. You can verify the status by running the following command and reviewing the features available for the detector with id `$GUARDDUTY_DETECTOR_ID`.

```bash
    aws guardduty get-detector --detector-id $GUARDDUTY_DETECTOR_ID | grep -i '"RUNTIME_MONITORING' -B 1 -A 20
```

The output of the command below shows that `RUNTIME_MONITORING` is enabled and configured for `ECS_FARGATE_AGENT_MANAGEMENT`:

```json
    {
        "Name": "RUNTIME_MONITORING",
        "Status": "ENABLED",
        "UpdatedAt": "2024-09-08T16:07:24+00:00",
        "AdditionalConfiguration": [
            {
                "Name": "EKS_ADDON_MANAGEMENT",
                "Status": "DISABLED",
                "UpdatedAt": "2024-09-08T14:36:17+00:00"
            },
            // Monitoring for ECS Fargate
            {
                "Name": "ECS_FARGATE_AGENT_MANAGEMENT",
                "Status": "ENABLED",
                "UpdatedAt": "2024-09-08T16:07:24+00:00"
            },
            {
                "Name": "EC2_AGENT_MANAGEMENT",
                "Status": "DISABLED",
                "UpdatedAt": "2024-09-08T14:36:17+00:00"
            }
        ]
    }
```

{{% expand "Expand if Amazon GuardDuty is not enabled" %}}
---

If Amazon GuardDuty is not already enabled, you can enable it by following these steps:

*⚠️ If an Amazon GuardDuty detector already exists, you will receive an error.*

```bash
    aws guardduty create-detector --enable
```

Save the detector id into the environment variable `DETECTOR_ID`:

```bash
    DETECTOR_ID=$(aws guardduty list-detectors | jq -r '.DetectorIds[]')
    echo $DETECTOR_ID
```

Update the detector to enable ECS runtime monitoring:

```bash
    aws guardduty update-detector \
        --detector-id $DETECTOR_ID \
        --features Name=RUNTIME_MONITORING,Status=ENABLED,AdditionalConfiguration="[{Name=ECS_FARGATE_AGENT_MANAGEMENT,Status=ENABLED}]"
```

Verify that ECS runtime monitoring is enabled:

```bash
    aws guardduty get-detector --detector-id $DETECTOR_ID | grep -i '"RUNTIME_MONITORING' -B 1 -A 20
```

{{% /expand %}}