---
title : "Tự động điều chỉnh quy mô với Amazon ECS"
date :  "`r Sys.Date()`" 
chapter : false
---

# Tự động điều chỉnh quy mô với Amazon ECS

{{% notice info %}}
**Yêu cầu tiên quyết**: Hoàn thành chương [Cơ bản](https://aws-fcj-ecs-workshop.github.io/Amazon-ECS-Immersion-Day/fundamentals/) trước khi bắt đầu bài thực hành này.
{{% /notice %}}

Dựa trên việc thiết lập môi trường cơ bản và cụm ECS trước đó, bài thực hành này sẽ khám phá cách triển khai tự động điều chỉnh quy mô hiệu quả với Amazon Elastic Container Service (Amazon ECS).

### Tìm hiểu về Tự động điều chỉnh quy mô trong Amazon ECS

#### Phân bổ tài nguyên trong Fargate

Trong Amazon ECS với Fargate, mỗi tác vụ chạy trong môi trường độc lập riêng. Khi tạo định nghĩa tác vụ, bạn phải chỉ định:
- Đơn vị CPU
- Phân bổ bộ nhớ

Phân bổ tài nguyên hợp lý là yếu tố quan trọng để đạt hiệu suất tối ưu. Các tác vụ của bạn cần có đủ tài nguyên để:
- Xử lý khối lượng công việc dự kiến
- Duy trì mức hiệu suất mong muốn
- Xử lý yêu cầu hiệu quả

#### Tối ưu hóa kích thước tác vụ

Để xác định phân bổ tài nguyên phù hợp:
1. Tiến hành đo lường hiệu suất
2. Thực hiện kiểm tra tải toàn diện
3. Giám sát các chỉ số hiệu suất chính
4. Phân tích mô hình sử dụng tài nguyên

Nếu tác vụ gặp vấn đề về hiệu suất hoặc hạn chế tài nguyên, hãy điều chỉnh phân bổ CPU và bộ nhớ cho phù hợp.

#### Mở rộng quy mô theo chiều ngang

Sau khi tối ưu hóa tài nguyên cho từng tác vụ, triển khai mở rộng theo chiều ngang bằng cách thêm nhiều phiên bản tác vụ. Phương pháp này được khuyến nghị cho các ứng dụng container hóa cloud-native vì:
- Cải thiện tính sẵn sàng
- Tăng cường khả năng chịu lỗi
- Phân phối tải hiệu quả

#### Tự động điều chỉnh quy mô dịch vụ

Amazon ECS cung cấp tính năng tự động điều chỉnh quy mô tác vụ thông qua Service Auto Scaling, sử dụng [Application Auto Scaling](https://docs.aws.amazon.com/autoscaling/application/userguide/what-is-application-auto-scaling.html).

Các yếu tố quan trọng cần xem xét để tự động điều chỉnh quy mô hiệu quả:
- Sử dụng các chỉ số tỷ lệ
- Đảm bảo các chỉ số tăng tuyến tính với tải
- Cấu hình ngưỡng điều chỉnh quy mô phù hợp

Ví dụ: Nếu tải của dịch vụ tăng gấp đôi trong khi duy trì cùng số lượng tác vụ, chỉ số được chọn cũng nên tăng gấp đôi.

{{% notice note %}}
Khi sử dụng Amazon ECS với các phiên bản EC2, hãy cân nhắc việc triển khai capacity providers để quản lý dung lượng phiên bản EC2. Bài thực hành này tập trung vào triển khai Fargate và không đề cập đến EC2 capacity providers.
{{% /notice %}}

{{< youtube YDbqnZ32NdM >}}