+++
title = "Test Pipeline"
date = 2024-05-14T00:38:32+07:00
weight = 5
chapter = false
pre = "<b>5. </b>"
+++

### Kiểm tra web application

Dán URL vào trình duyệt web để truy cập ứng dụng và kiểm tra tiêu đề cũng như màu sắc của ứng dụng web, những thứ mà chúng ta sẽ thay đổi.

Lấy URL của load balancer:

```
export RETAIL_ALB=$(aws elbv2 describe-load-balancers --name retail-store-ecs-ui \
 --query 'LoadBalancers[0].DNSName' --output text)

echo http://${RETAIL_ALB}
```

![Test-pipeline-1](/images/5/5-1.png?width=90pc)
![Test-pipeline-2](/images/5/5-2.png?width=90pc)

### Triển khai các thay đổi

Bây giờ, hãy chạy pipeline CI/CD đã tạo để thực hiện rolling deployment trong VS Code.

![Test-pipeline-3](/images/5/5-3-fix.png?width=90pc)

- Đổi tên banner giao diện chính với tiêu đề: **Retail Store Sample CodePipeline**
- Thay đổi màu của thanh điều hướng thành màu xanh lá (#0AD24A)

```
FILE="$HOME/Downloads/retail-store-sample-app-0.7.0/src/ui/src/main/resources/templates/fragments/layout.html"
```

{{% notice info %}}
Thay thế đường dẫn có tên **/Downloads/** bằng tên thư mục lưu trữ mã nguồn của bạn. Nếu gặp bất kỳ lỗi nào, bạn có thể chỉnh sửa nội dung đã hướng dẫn trực tiếp trong mã nguồn của mình.
{{% /notice %}}

```
sed -i 's|Retail Store Sample</a>|Retail Store Sample CodePipeline</a>|g' $FILE

sed -i 's|<nav class="navbar navbar-expand-lg navbar-light bg-light">|<nav class="navbar navbar-expand-lg navbar-light bg-light" style="background-color:#0AD24A !important">|g' $FILE
```

![Test-pipeline-4](/images/5/5-4-fix.png?width=90pc)

Xác minh xem các thay đổi đã được áp dụng thành công hay chưa bằng cách chạy lệnh sau:

```
cat $FILE | grep "style=\"background-color:#0AD24A"
cat $FILE | grep CodePipeline
```

![Test-pipeline-5](/images/5/5-10.png?width=90pc)

Commit các thay đổi với Github repository:

```
git init
git status
git add .
git commit -m "update UI and color banner"
git push
```

{{% notice info %}}
Có thể mất vài giây để pipeline bắt đầu.
{{% /notice %}}
![Test-pipeline-6](/images/5/5-11.png?width=90pc)

Đi đến bảng điều khiển AWS CodePipeline và nhấp vào pipeline để mở lần thực thi pipeline mới nhất.
![Test-pipeline-7](/images/5/5-6.png?width=90pc)
![Test-pipeline-8](/images/5/5-7.png?width=90pc)
![Test-pipeline-9](/images/5/5-8.png?width=90pc)

Khi giai đoạn Deploy bắt đầu, hãy nhấp vào liên kết sau để xem xét quá trình triển khai: [**Open ECS Deployment dashboard**](https://console.aws.amazon.com/ecs/v2/clusters/retail-store-ecs-cluster/services/retail-store-ui-cicd/deployments)

### Kiểm tra thay đổi của web application

Khi quá trình triển khai hoàn tất, truy cập ứng dụng web:

```
export RETAIL_ALB=$(aws elbv2 describe-load-balancers --name retail-store-ecs-ui \
 --query 'LoadBalancers[0].DNSName' --output text)

echo http://${RETAIL_ALB}:8080 ; echo
```

Xác nhận rằng tiêu đề đã thay đổi thành Retail Store Sample CodePipeline và thanh điều hướng hiện có nền màu xanh lá (#0AD24A).

#### - Sau đó

![Test-pipeline-12](/images/5/5-12.png?width=90pc)

#### - Trước đó

![Test-pipeline-13](/images/5/5-13.png?width=90pc)

Chúc mừng bạn! Thông qua bài lab này, bạn đã xây dựng thành công một pipeline CI/CD và triển khai một dịch vụ ECS mới bằng chiến lược triển khai rolling deployment.
