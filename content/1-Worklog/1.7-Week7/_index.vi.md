---
title: "Worklog Tuần 7"
date: 2026
weight: 1
chapter: false
pre: " <b> 1.7. </b> "
---


### Mục tiêu tuần 7:

* Tự động hóa khởi tạo hạ tầng bằng CloudFormation.
* Phân tích dữ liệu chi phí và mức sử dụng bằng S3, Glue và Athena.
* So sánh Savings Plans, Reserved Instances và Reserved DB Instances.
* Triển khai lưu trữ file Windows được quản lý bằng Amazon FSx tích hợp Active Directory.
* Bảo vệ ứng dụng web bằng AWS WAF phía trước ALB.

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc                                                                                                                                                                                   | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                            |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | --------------- | ----------------------------------------- |
| 1   | - Tự động hóa hạ tầng web app và RDS bằng CloudFormation. <br> - Tạo VPC, public subnet, IGW, route table, EC2, RDS, security group và script triển khai từ một template.                                                                                                | 13/05/2026 | 13/05/2026      | <https://000037.awsstudygroup.com/> |
| 2   | - Phân tích chi phí và hiệu năng bằng AWS Glue và Athena.<br> - Tạo S3 data/query bucket, Glue crawler/catalog và truy vấn Athena SQL cho phân tích chi phí kiểu CUR.<br>                                              | 14/05/2026 | 14/05/2026      | <https://000040.awsstudygroup.com/> |
| 3   | - Học Savings Plans, RI và Reserved DB Instances.<br> - So sánh phạm vi cam kết, hình thức thanh toán, đánh đổi chiết khấu, rủi ro dùng thiếu và cách đọc recommendation. | 15/05/2026 | 15/05/2026      | <https://000042.awsstudygroup.com/> |
| 4   | - Triển khai Amazon FSx for Windows File Server.<br> - Tích hợp FSx với Microsoft Active Directory, kiểm thử SMB từ Linux EC2 bằng cifs-utils/samba-client và xác minh đồng bộ file giữa các node.<br>                            | 16/05/2026 | 16/05/2026      | <https://000025.awsstudygroup.com/> |
| 5   | - Bảo vệ web application bằng AWS WAF. <br> - Đặt WAF trước ALB, kết nối ALB tới EC2 app và RDS database, sau đó kiểm tra luồng lọc traffic và bảo vệ ứng dụng.                 | 17/05/2026 | 17/05/2026      | <https://000026.awsstudygroup.com/> |


### Kết quả đạt được tuần 7:

* Tổng quan:

Trong tuần này, tôi tập trung vào chủ đề infrastructure as code, phân tích chi phí, mô hình cam kết, fsx và waf. Nội dung được tổng hợp từ worklog theo ngày và biên tập lại thành định dạng báo cáo theo tuần.

* Kiến thức đã học:

- Tự động hóa khởi tạo hạ tầng bằng CloudFormation.
- Phân tích dữ liệu chi phí và mức sử dụng bằng S3, Glue và Athena.
- So sánh Savings Plans, Reserved Instances và Reserved DB Instances.
- Triển khai lưu trữ file Windows được quản lý bằng Amazon FSx tích hợp Active Directory.
- Bảo vệ ứng dụng web bằng AWS WAF phía trước ALB.
* Thực hành:

- Chuyển từ thao tác hạ tầng thủ công sang template CloudFormation có thể lặp lại.
- Hiểu cách lưu trữ, catalog và truy vấn dữ liệu chi phí theo mô hình serverless.
- Thực hành các mẫu triển khai lưu trữ file được quản lý và bảo vệ web application.



