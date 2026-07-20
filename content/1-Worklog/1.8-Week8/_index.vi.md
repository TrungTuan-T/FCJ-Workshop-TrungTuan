---
title: "Worklog Tuần 8"
date: 2026
weight: 1
chapter: false
pre: " <b> 1.8. </b> "
---


### Mục tiêu tuần 8:

* Giới hạn delegated administrator bằng permission boundary.
* Định nghĩa hạ tầng full-stack bằng AWS CDK thay vì thao tác console hoặc YAML dài.
* Dịch chuyển database bằng AWS DMS với mục tiêu giảm downtime.
* Thiết kế IAM role condition dựa trên ràng buộc mạng thực tế.

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc                                                                                                                                                                                   | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                            |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | --------------- | ----------------------------------------- |
| 1   | - Giới hạn quyền người dùng bằng IAM Permission Boundary.<br> - Thiết kế boundary phân tầng cho DoiPho và Dev user để ngăn leo thang đặc quyền và chặn quyền RDS.                              | 18/05/2026 | 18/05/2026      | <https://000030.awsstudygroup.com/> |
| 2   | - Xây dựng hạ tầng cơ bản bằng AWS CDK.<br> - Dùng TypeScript CDK tạo VPC, public EC2, private RDS MariaDB, security group và UserData seed database.<br>                                              | 19/05/2026 | 19/05/2026      | <https://000038.awsstudygroup.com/> |
| 3   | - Thực hiện database schema conversion và migration.<br> - Dịch chuyển database của web app trên EC2 từ RDS MySQL sang RDS MariaDB bằng DMS endpoint và full load + CDC. | 20/05/2026 | 20/05/2026      | <https://000043.awsstudygroup.com/> |
| 4   | - Kiểm thử IAM Role và Condition.<br> - Điều chỉnh lab từ điều kiện IP sang aws:RequestedRegion do mạng trường dùng IP động, rồi kiểm thử allow ở Singapore và deny ở N. Virginia.<br>                            | 21/05/2026 | 21/05/2026      | <https://000044.awsstudygroup.com/> |


### Kết quả đạt được tuần 8:

* Tổng quan:

Trong tuần này, tôi tập trung vào chủ đề iam nâng cao, cdk, database migration và truy cập có điều kiện. Nội dung được tổng hợp từ worklog theo ngày và biên tập lại thành định dạng báo cáo theo tuần.

* Kiến thức đã học:

- Giới hạn delegated administrator bằng permission boundary.
- Định nghĩa hạ tầng full-stack bằng AWS CDK thay vì thao tác console hoặc YAML dài.
- Dịch chuyển database bằng AWS DMS với mục tiêu giảm downtime.
- Thiết kế IAM role condition dựa trên ràng buộc mạng thực tế.
* Thực hành:

- Hiểu permission boundary là trần quyền tối đa, không phải policy cấp quyền thông thường.
- Tạo hạ tầng bằng CDK và kết nối an toàn giữa tầng ứng dụng và database.
- Thực hành heterogeneous database migration và điều chỉnh IAM condition theo ràng buộc môi trường thật.


