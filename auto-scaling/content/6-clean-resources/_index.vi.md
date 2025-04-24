---
title: "Dọn dẹp tài nguyên"
date: "`r Sys.Date()`"
weight: 6
chapter: false
pre: "<b> 6. </b>"
---

### Xóa tính năng tự động điều chỉnh quy mô ứng dụng ECS

1. Điều hướng đến ECS cluster chứa service của bạn

2. Chọn service bạn muốn chỉnh sửa

3. Chọn nút **Update Service**

4. Trong phần Service Auto Scaling:
   - Đặt **Minimum number of tasks** thành 1
   - Đặt **Desired number of tasks** thành 1
   - Đặt **Maximum number of tasks** thành 1

5. Xóa các policy điều chỉnh quy mô hiện có:
   - Truy cập **Application Auto Scaling** trong AWS Console
   - Chọn **ECS Services** từ các tài nguyên có thể điều chỉnh quy mô
   - Tìm service của bạn và xóa các scaling policy liên quan
   - Xóa các target tracking policy
   - Xóa các step scaling policy

6. Xác minh việc xóa auto scaling:
   - Kiểm tra các metrics của service để xác nhận scaling đã bị vô hiệu hóa
   - Theo dõi số lượng task duy trì cố định ở mức 1
   - Rà soát CloudWatch alarms - xóa các alarm liên quan đến scaling policy đã xóa

7. Cập nhật service:
   - Chọn **Next step** cho đến khi đến bước review cuối cùng
   - Chọn **Update Service** để áp dụng thay đổi
   - Đợi service ổn định với cấu hình mới

8. Các thực hành tốt nhất:
   - Ghi lại các thay đổi trong tài liệu cơ sở hạ tầng
   - Giám sát hiệu suất service sau khi xóa auto scaling
   - Cân nhắc triển khai CloudWatch alarms để giám sát thủ công
   - Lưu giữ lịch sử triển khai để có thể rollback nếu cần

![alt text](/images/6-clean-resourcesimage.png)