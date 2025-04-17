---
title: "Kiểm tra tích hợp DynamoDB"
date: "`r Sys.Date()`"
weight: 4
chapter: false
pre: "<b> 3.4. </b>"
---

Phần này hướng dẫn cách xác minh rằng service `Carts` lưu trữ chính xác các mặt hàng mua sắm trong [Amazon DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html).

Đầu tiên, kiểm tra nội dung hiện tại của bảng DynamoDB. Nếu bạn chưa thêm bất kỳ mặt hàng nào vào giỏ hàng sau khi triển khai service `Carts`, bảng sẽ trống:

```bash
aws dynamodb scan --table-name retail-store-ecs-carts
```

Kết quả mong đợi cho bảng trống:

```json
{
    "Items": [],
    "Count": 0,
    "ScannedCount": 0,
    "ConsumedCapacity": null
}
```

Tiếp theo, truy cập ứng dụng cửa hàng bán lẻ để thêm mặt hàng vào giỏ hàng. Lấy URL ứng dụng bằng cách:

```bash
export RETAIL_ALB=$(aws elbv2 describe-load-balancers --name retail-store-ecs-ui \
    --query 'LoadBalancers[0].DNSName' --output text)

echo http://${RETAIL_ALB} ; echo
```

![Thêm mặt hàng vào Giỏ hàng](/images/3-iam-roles/3.4-test-dynamodb-integration/image.png)
*Hình 1. Thêm mặt hàng vào Giỏ hàng*

Sau khi thêm mặt hàng vào giỏ hàng, xác minh rằng chúng được lưu trữ trong DynamoDB bằng cách chạy lại lệnh scan bảng:

```bash
aws dynamodb scan --table-name retail-store-ecs-carts
```

Lệnh scan bây giờ sẽ hiển thị các mặt hàng được lưu trữ trong bảng DynamoDB:

```json
{
    "Items": [
        {
            "quantity": {
                "N": "1"
            },
            "unitPrice": {
                "N": "5100"
            },
            "itemId": {
                "S": "808a2de1-1aaa-4c25-a9b9-6612e8f29a38"
            },
            "id": {
                "S": "2b81546e-5dc8-47e6-be81-eca8dd704af9:808a2de1-1aaa-4c25-a9b9-6612e8f29a38"
            },
            "customerId": {
                "S": "2b81546e-5dc8-47e6-be81-eca8dd704af9"
            }
        },
        ...
    ],
    "Count": 3,
    "ScannedCount": 3,
    "ConsumedCapacity": null
}
```

Bây giờ bạn đã xác minh rằng tích hợp DynamoDB với service `Carts` đang hoạt động chính xác.