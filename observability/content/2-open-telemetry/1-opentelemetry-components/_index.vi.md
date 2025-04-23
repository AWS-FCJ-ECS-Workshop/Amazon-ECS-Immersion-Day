+++
title = " Các thành phần của OpenTelemetry"
date = 2020-05-14T00:38:32+07:00
weight = 2
chapter = false
pre = "<b>2.1 </b>"
+++

### OpenTelemetry Collector

OpenTelemetry Collector là một thành phần đa năng được thiết kế để xuất dữ liệu telemetry đến nhiều đích khác nhau, bao gồm Prometheus, AWS X-Ray và Amazon CloudWatch. AWS Distro for OpenTelemetry Collector đại diện cho bản phân phối được AWS hỗ trợ của OpenTelemetry Collector thượng nguồn. Thành phần được AWS duy trì này đảm bảo tích hợp đáng tin cậy với Amazon CloudWatch và nhiều backend được hỗ trợ khác, bao gồm các giải pháp ISV đối tác.

{{% notice info %}}
Tham khảo [tài liệu](https://aws-otel.github.io/docs/releases) của chúng tôi để tìm hiểu về các exporter khác nhau được hỗ trợ trong AWS Distro for OpenTelemetry.
{{% /notice %}}

OpenTelemetry Collector có thể được triển khai bằng nhiều mẫu khác nhau để phù hợp với yêu cầu kiến trúc của bạn:

**Sidecar Pattern:** Triển khai collector cùng với ứng dụng của bạn trên cùng một host, cho phép thu thập và xuất dữ liệu telemetry cục bộ.

**ECS Service Pattern:** Hoạt động như một dịch vụ collector tập trung, hợp nhất dữ liệu telemetry từ nhiều node ứng dụng. Để biết thông tin toàn diện về các mẫu triển khai và cấu hình, hãy tham khảo blog AWS về [deployment patterns](https://aws.amazon.com/blogs/opensource/deployment-patterns-for-the-aws-distro-for-opentelemetry-collector-with-amazon-elastic-container-service/).

### OpenTelemetry Protocol

Giao thức OpenTelemetry (OTLP) đóng vai trò là một đặc tả trung lập với nhà cung cấp để truyền dấu vết, số liệu và nhật ký. Nó cho phép chuyển đổi backend liền mạch bằng cách chỉ yêu cầu điều chỉnh cấu hình trên collector. OTLP thiết lập các cơ chế mã hóa, vận chuyển và phân phối dữ liệu tiêu chuẩn, đảm bảo khả năng tương thích trong tương lai và tích hợp đơn giản.

Để hiểu sâu hơn về các thành phần của OpenTelemetry, hãy tham khảo toàn bộ đặc tả. Bạn có thể tìm thấy đặc tả toàn diện về [components](https://opentelemetry.io/docs/concepts/components/) của OpenTelemetry..

### Đo đạc (Instrumentation)

OpenTelemetry cung cấp hỗ trợ rộng rãi cho việc tạo dữ liệu telemetry trên nhiều thư viện và framework khác nhau. Nó cho phép cả phương pháp đo đạc tự động và đo đạc thủ công để thu thập dữ liệu observability chi tiết:

**Đo đạc tự động (Auto-Instrumentation):** Thu thập dữ liệu telemetry tự động, bao gồm dấu vết, số liệu và nhật ký, mà không yêu cầu sửa đổi mã ứng dụng của bạn. Phương pháp này cung cấp tích hợp nhanh chóng với các thư viện và framework phổ biến, cho phép quan sát các yêu cầu HTTP và các hoạt động khác với cấu hình tối thiểu.

**Đo đạc thủ công (Manual Instrumentation):** Cung cấp khả năng kiểm soát chi tiết thông qua tích hợp trực tiếp các SDK OpenTelemetry vào codebase của bạn. Phương pháp này cho phép tạo các span, số liệu và nhật ký tùy chỉnh, mang lại sự linh hoạt để giám sát các hành vi cụ thể của ứng dụng và các đặc điểm hiệu suất chi tiết.

Các phương pháp bổ sung này có thể được kết hợp hiệu quả để giám sát các ứng dụng trên AWS App Runner, AWS Lambda, EC2, ECS, EKS trên EC2, AWS Fargate và các môi trường hybrid hoặc tại chỗ. Trong khi đo đạc tự động cung cấp phạm vi bao phủ toàn diện, đo đạc thủ công cho phép thu thập dữ liệu telemetry chuyên biệt.

{{% notice info %}}
Tham khảo [specification](https://opentelemetry.io/docs/specs/otel/) của OpenTelemetry để tìm hiểu thêm.
{{% /notice %}}
