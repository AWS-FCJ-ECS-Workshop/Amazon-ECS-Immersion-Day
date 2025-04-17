---
title: "Kiểm tra thiết lập GuardDuty"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: "<b> 4.1. </b>"
---

Amazon GuardDuty tạo một detector trong mỗi AWS Region để giám sát và xác định các mối đe dọa bảo mật tiềm ẩn trong tài khoản AWS của bạn. Detector đại diện cho dịch vụ GuardDuty và các cài đặt cấu hình của nó.

Trong workshop này, giám sát runtime của Amazon GuardDuty cho Amazon ECS đã được kích hoạt sẵn trong tài khoản của bạn. Xác minh trạng thái cấu hình bằng cách chạy lệnh sau để kiểm tra các tính năng của detector của bạn (được xác định bởi `$GUARDDUTY_DETECTOR_ID`):

```bash
aws guardduty get-detector --detector-id $GUARDDUTY_DETECTOR_ID | grep -i '"RUNTIME_MONITORING' -B 1 -A 20
```

Kết quả lệnh xác nhận rằng `RUNTIME_MONITORING` đã được kích hoạt với `ECS_FARGATE_AGENT_MANAGEMENT` được cấu hình:

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
{{% expand "Mở rộng nếu Amazon GuardDuty chưa được kích hoạt" %}}

Thực hiện các bước sau để kích hoạt Amazon GuardDuty nếu nó chưa được kích hoạt:

*⚠️ Lưu ý: Lệnh này sẽ trả về lỗi nếu detector GuardDuty đã tồn tại*

```bash
aws guardduty create-detector --enable
```

Lưu ID detector vào một biến môi trường:

```bash
DETECTOR_ID=$(aws guardduty list-detectors | jq -r '.DetectorIds[]')
echo $DETECTOR_ID
```

Kích hoạt giám sát runtime ECS cho detector:

```bash
aws guardduty update-detector \
    --detector-id $DETECTOR_ID \
    --features Name=RUNTIME_MONITORING,Status=ENABLED,AdditionalConfiguration="[{Name=ECS_FARGATE_AGENT_MANAGEMENT,Status=ENABLED}]"
```

Xác nhận giám sát runtime ECS đã được kích hoạt:

```bash
aws guardduty get-detector --detector-id $DETECTOR_ID | grep -i '"RUNTIME_MONITORING' -B 1 -A 20
```

{{% /expand %}}