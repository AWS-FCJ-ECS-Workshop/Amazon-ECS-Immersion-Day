+++
title = "Container Insights metrics"
date = 2020-05-14T00:38:32+07:00
weight = 1
chapter = false
pre = "<b>1.1 </b>"
+++

CloudWatch Container Insights thu thập các số liệu và nhật ký từ các ứng dụng và dịch vụ vi mô được chứa trong container của bạn. Nó sử dụng một tác nhân CloudWatch được chứa trong container để khám phá và thu thập dữ liệu hiệu suất từ tất cả các container đang chạy trong một cluster. Dữ liệu này sau đó được sử dụng để tạo ra các số liệu tổng hợp ở cấp độ cluster, service và task, bao gồm các số liệu về mức sử dụng tài nguyên cho CPU, bộ nhớ, đĩa và mạng.

Khi bạn cần các số liệu thống kê thường xuyên về dữ liệu tổng hợp theo thời gian, bạn nên sử dụng các số liệu (metrics) hơn là nhật ký (logs). Các số liệu rất lý tưởng để tạo cảnh báo hoặc biểu đồ trên dashboard.

### Khám phá các số liệu của Container Insights

Để khám phá các số liệu, trước tiên hãy mở bảng điều khiển CloudWatch Container Insights:

{{% button href="https://console.aws.amazon.com/cloudwatch/home#container-insights:infrastructure" %}}Mở bảng điều khiển Amazon CloudWatch{{% /button %}}

![image-2](/images/1/image-2.png?width=90pc)

Trong bảng điều khiển CloudWatch, chọn **Service: ECS** từ menu thả xuống Service và **Performance Monitoring** từ menu thả xuống thứ hai ở trên cùng. Sau đó, chọn **retail-store-ecs-cluster**. Cuối cùng, thay đổi ECS Cluster thành **ECS Services**.

![image-3](/images/1/image-3.png?width=90pc)
![image-4](/images/1/image-4.png?width=90pc)

Bạn có thể quan sát nhiều số liệu khác nhau như **CPU Utilization** (Mức sử dụng CPU), **Memory Utilization** (Mức sử dụng bộ nhớ), **Network RX/TX** (Lưu lượng mạng RX/TX), và **Ephemeral Storage Utilization** (Mức sử dụng bộ nhớ tạm thời). Các số liệu chính này và các dashboard tự động được tạo bởi CloudWatch Container Insights dựa trên **retail-store-ecs-cluster**. Vì các số liệu này cũng có sẵn trong CloudWatch Metrics, chúng có thể được sử dụng để tạo các dashboard tùy chỉnh.

{{% notice info %}}
Các số liệu của Amazon ECS Container Insights nằm trong namespace ECS/ContainerInsights. Để biết thông tin chi tiết về từng số liệu, vui lòng tham khảo tài liệu [tại đây](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Container-Insights-metrics-ECS.html).
{{% /notice %}}

### Khám phá bản đồ trực quan của ứng dụng

Để xem biểu diễn đồ họa các tài nguyên ECS và mối quan hệ của chúng, hãy nhấp vào **View in maps** (Xem trên bản đồ) ở menu phía trên bên phải.

![image-5](/images/1/image-5.png?width=90pc)
![image-6](/images/1/image-6.png?width=90pc)

Nếu bạn di chuột qua một trong các tài nguyên, bạn có thể xem thêm chi tiết như **CPU**, **memory utilization**, **network traffic**, and **disk utilization**.

![image-7](/images/1/image-7.png?width=90pc)
