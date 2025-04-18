---
title: "Storage"
date: "`r Sys.Date()`"
chapter: false
---

{{% notice info %}}
Hoàn thành các chương tiên quyết này trước khi bắt đầu bài thực hành: [Fundamentals](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/fundamentals/), [ECS Service Connect](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/networking/ecs-service-connect)
{{% /notice %}}

Amazon ECS hỗ trợ nhiều loại volume cho các task container:

*   Amazon EBS
*   Amazon EFS  
*   Amazon FSx for Windows File Server
*   Docker Volumes
*   Bind mounts

Để biết chi tiết toàn diện về các tùy chọn volume, tham khảo [tài liệu storage của Amazon ECS](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/using_data_volumes.html). Bảng sau so sánh các loại volume được sử dụng phổ biến nhất:

| Loại Volume | Hỗ trợ Launch Type | Tính bền vững của dữ liệu | Tính năng chính | Ứng dụng phổ biến |
|--------------|---------------------------|----------------------------------------------------------------------------|--------------------------------------------|-------------------------------------------------|
| Amazon EBS | Fargate, Amazon EC2 | Bền vững cho các task độc lập; Tạm thời cho các task được quản lý bởi service | Hiệu quả về chi phí, bền vững, hiệu năng cao | Khối lượng công việc giao dịch, xử lý log, công việc ETL |
| Amazon EFS | Fargate, Amazon EC2 | Hoàn toàn bền vững | Hệ thống tệp có thể mở rộng và chia sẻ | Phân tích dữ liệu, xử lý media, nội dung web |

#### Hiện trạng và Thách thức

Service Assets của bạn hiện đang hoạt động với một task duy nhất, tạo ra rủi ro về tính khả dụng. Ngoài ra, việc cập nhật hình ảnh sản phẩm yêu cầu triển khai lại toàn bộ ứng dụng, điều này không hiệu quả và tốn thời gian.

#### Đề xuất Cải tiến

Để nâng cao độ tin cậy và khả năng bảo trì của service Assets:

*   Mở rộng lên nhiều task để đảm bảo tính khả dụng cao
*   Triển khai Amazon EFS để lưu trữ chia sẻ hình ảnh sản phẩm

#### Lợi ích của tích hợp Amazon EFS:

*   Cho phép truy cập file đồng thời trên nhiều task
*   Đơn giản hóa việc quản lý hình ảnh mà không cần triển khai lại ứng dụng
*   Cung cấp lưu trữ bền vững trên các container instance

Kiến trúc cải tiến sẽ trông như thế này:

![Kiến trúc ECS với EFS mẫu](/images/image.png)
*Hình 1. Kiến trúc ECS với EFS*