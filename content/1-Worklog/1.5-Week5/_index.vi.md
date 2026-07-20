---
title: "Worklog Tuần 5"
date: 2026
weight: 1
chapter: false
pre: " <b> 1.5. </b> "
---


### Mục tiêu tuần 5:

* Kích hoạt tư duy giám sát bảo mật tập trung với Security Hub và AWS Config.
* Hiểu lộ trình chuyển từ triển khai EC2/ASG sang microservices trên ECS/Fargate.
* So sánh VPC Peering và Transit Gateway cho kết nối liên VPC.
* Xây dựng luồng tối ưu chi phí hướng sự kiện với CloudWatch, SNS và Lambda.

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc                                                                                                                                                                                   | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                            |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | --------------- | ----------------------------------------- |
| 1   | -  Học AWS Security Hub và AWS Config.<br> - Bật Security Hub CSPM, khảo sát AWS Foundational Security Best Practices, CIS và PCI DSS.                                                                                                | 04/05/2026 | 04/05/2026      | <https://000018.awsstudygroup.com/> |
| 2   | - Thiết kế triển khai microservices bằng ECS. <br> - Học Docker packaging, ECR image, Fargate task, task definition, private subnet, NAT Gateway và tách service.<br>                                              | 05/05/2026 | 05/05/2026      | <https://000016.awsstudygroup.com/><https://000067.awsstudygroup.com/> |
| 3   | - Thiết lập VPC Peering thủ công. <br> - Tạo hai VPC không trùng CIDR, cấu hình route/security group, kiểm thử kết nối private và rút ra giới hạn của peering. | 06/05/2026 | 06/05/2026      | <https://000019.awsstudygroup.com/> |
| 4   | - Xây dựng mạng hub-and-spoke bằng Transit Gateway.<br> - CKết nối bốn VPC qua TGW attachment, sửa lỗi thiếu propagation, kiểm thử ping liên VPC và dọn dẹp TGW để tránh phí cao.<br>                            | 06/05/2026 | 06/05/2026      | <https://000020.awsstudygroup.com/> |\
| 5   | - Tối ưu chi phí EC2 bằng Lambda automation.<br> - Dùng VPC Flow Logs, CloudWatch Metric Filter, Alarm, SNS và Lambda boto3 để stop EC2 khi phát hiện ICMP traffic.<br>                            | 07/05/2026 | 08/05/2026      | <https://000022.awsstudygroup.com/><https://000074.awsstudygroup.com/> |



### Kết quả đạt được tuần 5:

* Tổng quan:

Trong tuần này, tôi tập trung vào chủ đề tư thế bảo mật, container và kết nối liên vpc. Nội dung được tổng hợp từ worklog theo ngày và biên tập lại thành định dạng báo cáo theo tuần.

* Kiến thức đã học:

- Kích hoạt tư duy giám sát bảo mật tập trung với Security Hub và AWS Config.
- Hiểu lộ trình chuyển từ triển khai EC2/ASG sang microservices trên ECS/Fargate.
- So sánh VPC Peering và Transit Gateway cho kết nối liên VPC.
- Xây dựng luồng tối ưu chi phí hướng sự kiện với CloudWatch, SNS và Lambda.
* Thực hành:

- Kết nối các mảng bảo mật, container, networking mở rộng và automation trong cùng một tuần thực hành.
- Hiểu khi nào nên dùng VPC Peering và khi nào nên dùng Transit Gateway.
- Triển khai được luồng event-driven từ log đến hành động Lambda.


