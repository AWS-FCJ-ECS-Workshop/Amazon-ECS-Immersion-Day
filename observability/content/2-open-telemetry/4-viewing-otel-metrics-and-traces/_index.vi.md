+++
title = "Xem Metrics và Traces Otel"
date = 2020-05-14T00:38:32+07:00
weight = 2
chapter = false
pre = "<b>2.4 </b>"
+++

### Xác minh Traces và Metrics

Hãy xác minh thiết lập giám sát của bạn bằng cách truy cập ứng dụng thông qua AWS Load Balancer. Đầu tiên, lấy tên DNS của Load Balancer và sử dụng nó để tạo lưu lượng truy cập thử nghiệm đến ứng dụng của bạn.

```
RETAIL_ALB=$(aws elbv2 describe-load-balancers --name retail-store-ecs-ui \
 --query 'LoadBalancers[0].DNSName' --output text)

echo ${RETAIL_ALB}
```

Sử dụng tên DNS hiển thị ở trên để truy cập ứng dụng của bạn trong trình duyệt web. Để tạo meaningful traces:

1. Nhấp vào từng sản phẩm để xem chi tiết của chúng
2. Mở Catalog
3. Duyệt qua các danh mục sản phẩm khác nhau

{{% notice info %}}
Nếu thành phần giỏ hàng chưa được triển khai sau [Security Lab](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/security/), tránh thêm các mặt hàng vào giỏ hàng.
{{% /notice %}}

Các hành động này sẽ tạo ra các trace cho các dịch vụ UI, Catalog và Assets. Nếu bạn không thấy traces cho tất cả các dịch vụ ngay lập tức, hãy đợi vài phút và làm mới bảng điều khiển CloudWatch, vì có thể có một chút chậm trễ trong quá trình xử lý dữ liệu.

Để xem xét dữ liệu traces, metrics và logs, hãy điều hướng đến bảng điều khiển CloudWatch bằng liên kết bên dưới.

{{% button href="https://console.aws.amazon.com/cloudwatch/home#container-insights:infrastructure" %}}Mở bảng điều khiển Amazon CloudWatch{{% /button %}}

### Kiểm tra AWS X-Ray Trace Map

Trong CloudWatch, hãy chuyển đến **X-Ray traces** và chọn **Trace map**. Bạn sẽ thấy một bản đồ dịch vụ tương tự như hình bên dưới.

![image](/images/5/image.png?width=90pc)

Trace Map hiển thị các mối quan hệ và tương tác giữa các microservice và thành phần của bạn. Hình ảnh trực quan này giúp bạn hiểu cách các yêu cầu luồng qua kiến trúc ứng dụng của bạn. Bạn sẽ nhận thấy tên ui-application trong trace map, tương ứng với _OTEL_SERVICE_NAME_ mà chúng ta đã cấu hình trước đó.

Bên dưới Trace Map là **Segment Timeline**, hiển thị các Segments khác nhau mà trace chứa.

![image-1](/images/5/image-1.png?width=90pc)
![image-2](/images/5/image-2.png?width=90pc)

{{% notice info %}}
Để tìm hiểu thêm về các khái niệm và khả năng của AWS X-Ray, hãy truy cập [tài liệu](https://docs.aws.amazon.com/xray/latest/devguide/xray-concepts.html) về AWS X-Ray..
{{% /notice %}}

### Xem Metrics trong CloudWatch

CloudWatch Metrics cho phép bạn giám sát hiệu suất của ứng dụng Java đã được đo đạc thông qua nhiều số liệu OpenTelemetry khác nhau. Chúng bao gồm mức sử dụng bộ nhớ JVM, số lượng thread và thống kê thu gom rác, rất cần thiết để giám sát tình trạng ứng dụng và tối ưu hóa việc sử dụng tài nguyên.

Để xem xét số liệu về số lượng thread:

1. Điều hướng đến **All Metrics** từ thanh bên trái trong** Metrics**
2. Chọn namespace tùy chỉnh _ECS/AWSOTel/Application_
3. Tìm kiếm số liệu _process.runtime.jvm.threads.count_

### Xem EMF Logs trong CloudWatch Logs

Cấu hình AWS Distro for OpenTelemetry (ADOT) sử dụng Embedded Metric Format (EMF) làm exporter, tự động chuyển đổi các số liệu ứng dụng của bạn thành số liệu CloudWatch. Các số đo này cũng có sẵn dưới dạng logs trong CloudWatch Logs, cung cấp thêm ngữ cảnh cho hiệu suất ứng dụng của bạn.

Để xem xét cấu trúc của một mục nhật ký EMF:

1. Điều hướng đến **Log groups** từ thanh bên trái trong **Logs**
2. Tìm Log Group _/aws/ecs/application/metrics_
3. Nhấp vào **Log Group** để khám phá các log stream

### Tìm kiếm các Mục Log Cụ thể

Để tìm các mục nhật ký liên quan đến số liệu thread JVM:

1. Chọn **Logs Insights** từ thanh bên trái trong **Logs**
2. Chọn log group /aws/ecs/application/metrics
3. Nhập và chạy truy vấn sau để lọc các số liệu thread JVM:

```
fields @timestamp, @message, @logStream, @log
| filter @message like /jvm\.threads/
| sort @timestamp desc
| limit 10
```

### Tìm hiểu về EMF Log Metrics

Kết quả truy vấn sẽ hiển thị các số liệu ở định dạng nhật ký, bao gồm các dimension và thông tin sự kiện AWS. Các thành phần chính bao gồm:

- **Namespace:** Một chuỗi đại diện cho namespace CloudWatch của số liệu
- **Dimensions:** Một mảng các _DimensionSet_
- **Metrics:** Một mảng các đối tượng _MetricDefinition_ objects

{{% notice info %}}
Để biết chi tiết toàn diện về định dạng EMF, hãy tham khảo [Đặc tả Định dạng Số liệu Nhúng CloudWatch](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Embedded_Metric_Format_Specification.html).
{{% /notice %}}
