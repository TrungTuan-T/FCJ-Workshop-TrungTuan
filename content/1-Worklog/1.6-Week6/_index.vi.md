---
title: "Worklog Tuần 6"
date: 2026
weight: 1
chapter: false
pre: " <b> 1.6. </b> "
---


### Mục tiêu tuần 6:

* Tự động hóa triển khai ứng dụng lên EC2 bằng CodePipeline và CodeDeploy.
* Giới hạn thao tác EC2 bằng IAM condition dựa trên tag.
* Cài Grafana và kết nối với CloudWatch metrics thông qua IAM Role.
* Quản lý EC2 fleet bằng Systems Manager và thu thập memory metrics để right-sizing.
* Phân tích truy cập S3 và mã hóa bằng KMS, CloudTrail và Athena.
### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc                                                                                                                                                                                   | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                            |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | --------------- | ----------------------------------------- |
| 1   | - Triển khai ứng dụng lên EC2 bằng AWS CodePipeline. <br> - Kết nối GitHub, CodePipeline, S3 artifact, CodeDeploy, AppSpec hook, script PM2 và CodeDeploy agent trên EC2.                                                                                             | 08/05/2026 | 08/05/2026      | <https://000017.awsstudygroup.com/><https://000023.awsstudygroup.com/>   |
| 2   | - Quản lý quyền EC2 bằng tag thông qua IAM.<br> - Tạo policy bắt buộc Environment=Test khi tạo máy và chỉ cho phép start/stop/terminate với instance có tag phù hợp.<br>                                              | 09/05/2026 | 09/05/2026      | <https://000028.awsstudygroup.com/> |
| 3   | - Cài Grafana trên EC2 và kết nối CloudWatch.<br> - Tạo VPC/SG/EC2, mở cổng 3000, gắn IAM Role và trực quan hóa CPUUtilization trên Grafana. | 10/05/2026 | 10/05/2026      | <https://000029.awsstudygroup.com/> |
| 4   | - Quản lý patch và chạy lệnh bằng AWS Systems Manager. <br> - Gắn AmazonSSMManagedInstanceCore, xử lý lỗi Managed Nodes offline và chạy command hàng loạt trên instance. <br>                            | 10/05/2026 | 10/05/2026      | <https://000031.awsstudygroup.com/> |
| 5   | - Chọn size EC2 phù hợp bằng CloudWatch Agent. <br> - Cài CloudWatch Agent, thu thập RAM metrics và chuẩn bị dữ liệu cho Compute Optimizer/Cost Explorer.                                     | 11/05/2026 | 11/05/2026      | <https://000032.awsstudygroup.com/> |
| 6   | - Mã hóa dữ liệu nghỉ bằng AWS KMS và audit truy cập. <br> - Dùng CloudTrail data events và Athena SQL để phát hiện truy cập, sau đó kiểm chứng KMS deny khi thiếu quyền Decrypt.                                                      | 12/05/2026 | 12/05/2026      | <https://000033.awsstudygroup.com/> |

### Kết quả đạt được tuần 6:
* Tổng quan:

Trong tuần này, tôi tập trung vào chủ đề ci/cd, iam governance, monitoring, systems management và mã hóa. Nội dung được tổng hợp từ worklog theo ngày và biên tập lại thành định dạng báo cáo theo tuần.

* Kiến thức đã học:

- Tự động hóa triển khai ứng dụng lên EC2 bằng CodePipeline và CodeDeploy.
- Giới hạn thao tác EC2 bằng IAM condition dựa trên tag.
- Cài Grafana và kết nối với CloudWatch metrics thông qua IAM Role.
- Quản lý EC2 fleet bằng Systems Manager và thu thập memory metrics để right-sizing.
- Phân tích truy cập S3 và mã hóa bằng KMS, CloudTrail và Athena.
* Thực hành:

- Xây dựng được luồng CI/CD và xử lý các lỗi thường gặp ở CodeDeploy/IAM/AppSpec.
- Thực hành governance thực tế bằng tag, IAM condition và kiểm soát mã hóa.
- Tăng khả năng vận hành hệ thống bằng Grafana, CloudWatch Agent và Systems Manager.


