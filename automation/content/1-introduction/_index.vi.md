+++
title = "Giới thiệu"
date = 2020-05-14T00:38:32+07:00
weight = 1
chapter = false
pre = "<b>1. </b>"
+++

{{% notice info %}}
**Điều kiện tiên quyết:** Hoàn thành chương [Cơ bản](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/fundamentals/) trước khi bắt đầu lab này.
{{% /notice %}}

### Triển khai Rolling của ECS là gì?

Triển khai Rolling là một chiến lược triển khai thay thế dần các phiên bản cũ của ứng dụng bằng các phiên bản mới, giảm thiểu thời gian ngừng hoạt động và đảm bảo tính khả dụng liên tục của dịch vụ.

Khi sử dụng kiểu triển khai cập nhật rolling trong Amazon ECS, nền tảng sẽ thay thế các tác vụ đang chạy bằng các tác vụ mới dựa trên cấu hình dịch vụ bạn đã xác định. Hành vi này được kiểm soát bởi hai tham số chính:

- minimumHealthyPercent: Số lượng tác vụ tối thiểu phải ở trạng thái RUNNING trong quá trình triển khai.

- maximumPercent: Số lượng tác vụ tối đa (ở trạng thái RUNNING hoặc PENDING) được phép trong quá trình triển khai.

#### Ví dụ

Nếu số lượng tác vụ mong muốn của bạn là 4:

- Với minimumHealthyPercent = 50, ECS có thể dừng tối đa 2 tác vụ cùng một lúc trước khi bắt đầu các tác vụ mới.

- Với maximumPercent = 200, ECS có thể bắt đầu tối đa 4 tác vụ mới trước khi dừng các tác vụ cũ.

{{% notice note %}}
Tốc độ triển khai phụ thuộc vào các giá trị của minimumHealthyPercent và maximumPercent. Nếu dịch vụ ECS của bạn được tích hợp với Application Load Balancer (ALB), các cài đặt kiểm tra sức khỏe cũng ảnh hưởng đến thời gian triển khai.
Để biết các mẹo về tối ưu hóa tốc độ triển khai, hãy tham khảo: [bài đăng này về cách tăng tốc độ triển khai container Amazon ECS](https://nathanpeck.com/speeding-up-amazon-ecs-container-deployments/).
{{% /notice %}}

### Triển khai Rolling của ECS với AWS CodePipeline

Bạn có thể tự động hóa việc triển khai rolling lên ECS bằng AWS CodePipeline. Dưới đây là hướng dẫn từng bước để thiết lập:

1. **Xác định Task Definition và Service ECS của bạn**

   Bắt đầu bằng cách xác định **task definition** và **service** ECS của bạn:

   - Chỉ định image container, giới hạn tài nguyên, mạng và cài đặt môi trường.

   - Định cấu hình các tùy chọn cấp dịch vụ như số lượng tác vụ mong muốn và cân bằng tải.

2. **Thiết lập AWS CodePipeline của bạn**

   Tạo một pipeline với các stage sau:

   - Source (Nguồn)

   - Build (Xây dựng)

   - Deploy (Triển khai)

3. **Source Stage (Giai đoạn Nguồn)**

   Định cấu hình CodePipeline để kết nối với kho lưu trữ mã nguồn của bạn (ví dụ: **GitHub**, **AWS CodeCommit**).

   Mỗi khi phát hiện thấy thay đổi mã, pipeline sẽ tự động kích hoạt một quy trình triển khai mới.

4. **Build Stage (Giai đoạn Xây dựng)**

   Sử dụng **AWS CodeBuild** (hoặc một công cụ xây dựng khác) để:

   - Biên dịch mã của bạn

   - Chạy các thử nghiệm tự động (tùy chọn)

   - Xây dựng và gắn thẻ Docker image của bạn

   - Đẩy image lên **Amazon ECR** (Elastic Container Registry)

5. **Deploy Stage (Giai đoạn Triển khai)**

   Sử dụng **AWS CodeDeploy** để quản lý việc triển khai rolling:

   - Liên kết CodeDeploy với dịch vụ ECS của bạn

   - Định cấu hình một deployment group với các cài đặt cập nhật rolling (ví dụ: minimumHealthyPercent, maximumPercent)

   - CodeDeploy sẽ xử lý việc thay thế tác vụ và đảm bảo lưu lượng truy cập được định tuyến chính xác thông qua load balancer.

6. **Testing & Approval (Thử nghiệm & Phê duyệt) (Tùy chọn)**

   Thêm các stage bổ sung để tăng cường khả năng kiểm soát và chất lượng:

   - Chạy các thử nghiệm tích hợp hoặc smoke test sau khi triển khai

   - Thêm các bước phê duyệt thủ công để yêu cầu sự kiểm tra của con người trước khi quá trình triển khai tiếp tục.

7. **Monitoring & Rollback (Giám sát & Khôi phục)**

   Bật tính năng giám sát và cảnh báo để có khả năng hiển thị theo thời gian thực:

   - Sử dụng **CloudWatch Logs**, **ECS Service Events** và **CloudWatch Alarms**

   - Nếu phát hiện sự cố, CodeDeploy có thể **tự động khôi phục** về phiên bản hoạt động trước đó.

8. **Continuous Improvement (Cải tiến Liên tục)**

   Sau mỗi lần triển khai:

   - Xem xét các số liệu, nhật ký và phản hồi

   - Xác định các nút thắt hoặc lỗi

   - Lặp lại và cải thiện chiến lược triển khai của bạn

Bằng cách tuân theo phương pháp có cấu trúc này, bạn có thể xây dựng một pipeline triển khai rolling tự động, đáng tin cậy bằng cách sử dụng AWS CodePipeline và Amazon ECS. Thiết lập này cho phép bạn triển khai các bản cập nhật một cách an toàn, giảm rủi ro và duy trì tính khả dụng cao với nỗ lực tối thiểu.

![Deployment-Rolling](/images/1/Deployment-Rolling.png?width=90pc)
