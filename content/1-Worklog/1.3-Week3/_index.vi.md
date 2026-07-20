---
title: "Worklog Tuần 3"
date: 2026
weight: 1
chapter: false
pre: " <b> 1.3. </b> "
---


### Mục tiêu tuần 3:

* Thiết lập chốt chặn tài chính để tránh phát sinh chi phí AWS ngoài ý muốn.
* Phân biệt Cost Budget, Usage Budget, RI Budget và Savings Plans Budget.
* Xây dựng kỹ năng quan sát hệ thống với CloudWatch metrics, logs, alarms, dashboards và SNS.
* Thực hành đọc metrics bằng search expression, metric math và dashboard widget.

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc                                                                                                                                                                                   | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                            |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | --------------- | ----------------------------------------- |
| 1   | - Tạo AWS Cost Budget. <br> - Cấu hình theo dõi unblended cost cho toàn bộ dịch vụ và cảnh báo nhiều mức 50%, 80%, 100%.                                              | 29/04/2026 | 29/04/2026      | <https://000007.awsstudygroup.com/> |
| 2   | - Tạo AWS Usage Budget.<br> - Theo dõi EC2 running hours để bảo vệ Free Tier và hiểu rủi ro chi phí ngầm khi quên xóa tài nguyên.<br>                                              | 30/04/2026 | 30/04/2026      | <https://000007.awsstudygroup.com/> |
| 3   | - Tìm hiểu RI Budget và Savings Plans Budget.<br> - So sánh giám sát mức sử dụng reservation với giám sát cam kết compute trong quản trị chi phí doanh nghiệp. | 31/04/2026 | 31/04/2026      | <https://0000042.awsstudygroup.com/> |
| 4   | - Bắt đầu Amazon CloudWatch Workshop. <br> - Triển khai hạ tầng EC2 bằng CloudFormation và nhận diện lỗi region/vCPU quota khi tạo stack.<br>                            | 01/05/2026 | 01/05/2026      | <https://000008.awsstudygroup.com/> <https://0000036.awsstudygroup.com/>  |



### Kết quả đạt được tuần 3:

* Tổng quan:

Trong tuần này, tôi tập trung vào chủ đề chốt chặn chi phí và nền tảng quan sát hệ thống. Nội dung được tổng hợp từ worklog theo ngày và biên tập lại thành định dạng báo cáo theo tuần.

* Kiến thức đã học:

- Thiết lập chốt chặn tài chính để tránh phát sinh chi phí AWS ngoài ý muốn.
- Phân biệt Cost Budget, Usage Budget, RI Budget và Savings Plans Budget.
- Xây dựng kỹ năng quan sát hệ thống với CloudWatch metrics, logs, alarms, dashboards và SNS.
- Thực hành đọc metrics bằng search expression, metric math và dashboard widget.
Thực hành:

- Cấu hình được cảnh báo ngân sách theo cả góc nhìn chi phí và mức sử dụng.
Hiểu vì sao cần tạo financial guardrails trước khi thực hành nhiều lab.
- Bắt đầu làm việc với CloudWatch metrics và biết cách xử lý giới hạn khi triển khai hạ tầng.


