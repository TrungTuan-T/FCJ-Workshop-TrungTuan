---
title: "Worklog Tuần 4"
date: 2026
weight: 1
chapter: false
pre: " <b> 1.4. </b> "
---


### Mục tiêu tuần 4:

* Xây dựng phân giải DNS hai chiều giữa môi trường on-premises giả lập và AWS.
* Sử dụng AWS CLI để quản lý S3, SNS, IAM, VPC và EC2.
* Thiết kế quy trình backup và restore bằng AWS Backup.
* Thực hành dọn dẹp tài nguyên theo thứ tự phụ thuộc để kiểm soát chi phí.

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc                                                                                                                                                                                   | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                            |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | --------------- | ----------------------------------------- |
| 1   | - ITriển khai kiến trúc Route 53 Resolver. <br> - Tạo on-premises VPC và AWS VPC giả lập, cấu hình VPC peering, route table, inbound/outbound endpoint và resolver rule.                                                                                                   | 01/05/2026 | 01/05/2026      | <https://000010.awsstudygroup.com/> |
| 2  | - Thực hành quản lý tài nguyên bằng AWS CLI.<br> -  Cài AWS CLI v2, cấu hình credential, quản lý S3/SNS/IAM và tạo VPC/EC2 bằng dòng lệnh.<br>                                              | 02/05/2026 | 02/05/2026      | <https://000011.awsstudygroup.com/> |
| 3   | - Tạo AWS Backup plan và backup vault.<br> - Dùng tag để gán EC2 vào backup rule và cấu hình IAM/SNS permission cho thông báo backup. | 02/05/2026 | 03/05/2026      | <https://000013.awsstudygroup.com/> |
| 4   | - Kiểm thử restore và quy trình cleanup.<br> - Hiểu recovery point, restore không ghi đè, idempotency token và thứ tự dọn dẹp tài nguyên liên quan đến backup.<br>                            | 03/05/2026 | 04/05/2026      | <https://000013.awsstudygroup.com/> |

### Kết quả đạt được tuần 4:

* Tổng quan:

Trong tuần này, tôi tập trung vào chủ đề dns, tự động hóa cli, backup và khôi phục. Nội dung được tổng hợp từ worklog theo ngày và biên tập lại thành định dạng báo cáo theo tuần.

* Kiến thức đã học:

- Xây dựng phân giải DNS hai chiều giữa môi trường on-premises giả lập và AWS.
- Sử dụng AWS CLI để quản lý S3, SNS, IAM, VPC và EC2.
- Thiết kế quy trình backup và restore bằng AWS Backup.
- Thực hành dọn dẹp tài nguyên theo thứ tự phụ thuộc để kiểm soát chi phí.
* Thực hành:

- Xây dựng được mô hình DNS forwarding thủ công giữa hai mạng hybrid giả lập.
- Sử dụng AWS CLI để thao tác hạ tầng thay vì chỉ dùng console.
- Cấu hình backup tự động và hiểu cơ chế restore cho workload EC2.


