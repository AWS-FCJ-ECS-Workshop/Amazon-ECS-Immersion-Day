---
title: "Review GuardDuty Setup"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: "<b> 4.1. </b>"
---

Amazon GuardDuty creates a detector in each AWS Region to monitor and identify potential security threats in your AWS account. The detector represents the GuardDuty service and its configuration settings.

For this workshop, Amazon GuardDuty runtime monitoring for Amazon ECS is pre-enabled in your account. Verify the configuration status by executing this command to examine the features of your detector (identified by `$GUARDDUTY_DETECTOR_ID`):

```bash
aws guardduty get-detector --detector-id $GUARDDUTY_DETECTOR_ID | grep -i '"RUNTIME_MONITORING' -B 1 -A 20
```

The command output confirms that `RUNTIME_MONITORING` is enabled with `ECS_FARGATE_AGENT_MANAGEMENT` configured:

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
---
{{% expand "Expand if Amazon GuardDuty is not enabled" %}}

Follow these steps to enable Amazon GuardDuty if it isn't already activated:

*⚠️ Note: This command will return an error if a GuardDuty detector exists*

```bash
aws guardduty create-detector --enable
```

Store the detector ID in an environment variable:

```bash
DETECTOR_ID=$(aws guardduty list-detectors | jq -r '.DetectorIds[]')
echo $DETECTOR_ID
```

Enable ECS runtime monitoring for the detector:

```bash
aws guardduty update-detector \
    --detector-id $DETECTOR_ID \
    --features Name=RUNTIME_MONITORING,Status=ENABLED,AdditionalConfiguration="[{Name=ECS_FARGATE_AGENT_MANAGEMENT,Status=ENABLED}]"
```

Confirm ECS runtime monitoring is enabled:

```bash
aws guardduty get-detector --detector-id $DETECTOR_ID | grep -i '"RUNTIME_MONITORING' -B 1 -A 20
```

{{% /expand %}}