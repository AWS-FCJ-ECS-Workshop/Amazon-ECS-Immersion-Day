+++
title = "Khả năng quan sát với Amazon ECS"
date = 2024
weight = 1
chapter = false
+++

# Observability with Amazon ECS

Khả năng quan sát là khả năng liên tục tạo và khám phá những thông tin chi tiết hữu ích dựa trên các tín hiệu từ hệ thống đang được theo dõi. Nói cách khác, khả năng quan sát cho phép người dùng hiểu được trạng thái của hệ thống từ các đầu ra bên ngoài của nó và thực hiện các hành động phù hợp. Ba trụ cột của khả năng quan sát là số liệu (metrics), nhật ký (logs) và dấu vết (traces):

#### Số liệu (Metrics)

- Số liệu biểu diễn dữ liệu số được đo lường theo các khoảng thời gian. Chúng tận dụng mô hình hóa và dự đoán toán học để hiểu hành vi của hệ thống ở cả hiện tại và tương lai.
- Chúng hữu ích để xác định xu hướng và cho phép mô hình hóa và dự đoán toán học.

#### Nhật ký (Logs)

- Nhật ký bao gồm các bản ghi được đánh dấu thời gian, bất biến, ghi lại các sự kiện rời rạc khi chúng xảy ra theo thời gian. Chúng có giá trị trong việc phát hiện các hành vi mới nổi và khó lường.
- Chúng đặc biệt hữu ích để khám phá các mô hình hành vi mới nổi và khó lường.

#### Dấu vết (Traces)

- Dấu vết mô tả một chuỗi các sự kiện phân tán, liên kết với nhau, vạch ra hành trình đầu cuối của một yêu cầu thông qua một hệ thống phân tán. Chúng cung cấp thông tin chi tiết, chẳng hạn như độ trễ, về đường đi của một yêu cầu và cấu trúc của nó.
- Chúng cung cấp khả năng hiển thị cả đường đi mà một yêu cầu đã đi qua cũng như cấu trúc của một yêu cầu.

![Observability](/images/1/Observability.png?width=90pc)

Tóm lại, chúng ta có thể chia khả năng quan sát thành ba thành phần chính: CloudWatch Metrics, CloudWatch Logs và AWS X-Ray. Cùng nhau, chúng tạo thành một giải pháp khả năng quan sát toàn diện trên AWS, bao gồm giám sát số liệu, quản lý nhật ký và theo dõi phân tán. Các trụ cột này phối hợp với nhau để cung cấp cho người dùng những hiểu biết sâu sắc về hành vi, hiệu suất và độ tin cậy của môi trường và ứng dụng AWS của họ.
