---
title : "Service Auto Scaling"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre: "<b> 2. </b>"
---

#### Service Auto Scaling

Amazon ECS tích hợp với Amazon CloudWatch để mở rộng quy mô các service ECS dựa trên các chỉ số thời gian thực. ECS truyền các chỉ số đến CloudWatch mỗi phút, cho phép giám sát và đưa ra quyết định mở rộng chính xác. Khi các chỉ số vượt quá ngưỡng đã định trong chính sách mở rộng của bạn, cảnh báo CloudWatch sẽ kích hoạt điều chỉnh số lượng task mong muốn trong service của bạn. Quá trình này tự động tăng công suất trong thời điểm nhu cầu cao và giảm xuống khi nhu cầu giảm.

![Demonstrating Service Auto Scaling Work](/images/2-service-auto-scaling/image.png)
*Hình 1: Quy trình Service Auto Scaling*

Amazon ECS cung cấp ba chiến lược mở rộng service:

![Three type of scaling strategies](/images/2-service-auto-scaling/image-1.png)
*Hình 2: Các loại chiến lược mở rộng ECS*

**Target Tracking Scaling**
- Duy trì một chỉ số cụ thể ở giá trị mục tiêu bằng cách tự động điều chỉnh số lượng task
- Yêu cầu cấu hình và bảo trì tối thiểu
- Được khuyến nghị cho hầu hết các khối lượng công việc production
- Ví dụ: Duy trì mức sử dụng CPU trung bình ở 50%

**Step Scaling**
- Cung cấp kiểm soát chi tiết về các hành động mở rộng
- Cho phép lựa chọn chỉ số tùy chỉnh và giá trị ngưỡng
- Cho phép điều chỉnh tài nguyên cụ thể ở mỗi bước
- Có thể cấu hình thời gian đánh giá vi phạm cho cảnh báo chỉ số
- Phù hợp với các yêu cầu mở rộng phức tạp

**Scheduled Scaling**
- Mở rộng tài nguyên dựa trên các mẫu tải có thể dự đoán
- Lý tưởng cho các ứng dụng có biến động lưu lượng đã biết
- Cho phép phân bổ tài nguyên chủ động
- Ví dụ: Tăng công suất trước giờ cao điểm hàng ngày
- Giảm chi phí bằng cách thu hẹp quy mô trong thời điểm nhu cầu thấp

Mỗi chiến lược mở rộng giúp tối ưu hóa việc sử dụng tài nguyên và hiệu suất ứng dụng trong khi quản lý chi phí hiệu quả. Chọn chiến lược phù hợp dựa trên mô hình khối lượng công việc và yêu cầu vận hành của bạn.