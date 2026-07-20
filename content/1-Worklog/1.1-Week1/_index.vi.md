---
title: "Worklog Tuần 1"
date: 2026
weight: 1
chapter: false
pre: " <b> 1.1. </b> "
---


### Mục tiêu tuần 1:

* Nắm tổng quan lộ trình thực tập và vai trò của các dịch vụ AWS trong triển khai hệ thống thực tế.
* Thiết lập mô hình truy cập an toàn hơn bằng IAM User, Group, Policy, Role và ủy quyền truy cập Billing.
* Xây dựng nền tảng mạng với VPC, public/private subnet, route table, security group và VPC endpoint.
* Khởi chạy EC2 instance và thực hành triển khai ứng dụng cơ bản trên môi trường Linux và Windows

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc                                                                                                                                                                                   | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                            |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | --------------- | ----------------------------------------- |
| 1   | - Học các khái niệm IAM cốt lõi: root account, account ID, group, policy, role và nguyên tắc đặc quyền tối thiểu. <br> - Tạo admin group/user, cấu hình Admin role, tạo OperatorUser và kiểm thử Switch Role.                                                                                           | 22/04/2026   | 22/04/2026      | <https://000002.awsstudygroup.com/> |
| 2   | - Học các khái niệm trong Amazon VPC và AWS Site-to-Site VPN workshop. <br>Thiết kế public/private subnet, internet gateway, route table, bastion access và VPC endpoint để truy cập dịch vụ AWS nội bộ.<br>                                            | 23/04/2026   | 23/04/2026      | <https://000003.awsstudygroup.com/> |
| 3   | - Thực hành nền tảng Amazon EC2. <br> - Tạo mạng VPC, khởi chạy EC2 Linux/Windows, cấu hình security group, cài LAMP/Node.js và triển khai ứng dụng CRUD. | 24/04/2026   | 24/04/2026      | <https://000004.awsstudygroup.com/> |
| 4   | - Xử lý lỗi kết nối máy chủ và dọn dẹp tài nguyên. <br>- Khắc phục lỗi timeout bằng cách kiểm tra Security Group và firewall hệ điều hành, sau đó terminate EC2/VPC để tránh phát sinh chi phí.<br>                  | 24/04/2026   | 24/04/2026      | <https://000004.awsstudygroup.com/> |


### Kết quả đạt được tuần 1:

* Tổng quan:

Trong tuần này, tôi tập trung vào chủ đề làm quen với aws, iam, vpc và ec2. Nội dung được tổng hợp từ worklog theo ngày và biên tập lại thành định dạng báo cáo theo tuần.

* Kiến thức đã học:

- Nắm tổng quan lộ trình thực tập và vai trò của các dịch vụ AWS trong triển khai hệ thống thực tế.
- Thiết lập mô hình truy cập an toàn hơn bằng IAM User, Group, Policy, Role và ủy quyền truy cập Billing.
- Xây dựng nền tảng mạng với VPC, public/private subnet, route table, security group và VPC endpoint.
- Khởi chạy EC2 instance và thực hành triển khai ứng dụng cơ bản trên môi trường Linux và Windows.
* Thực hành:

- Xây dựng được mô hình truy cập tài khoản AWS ban đầu mà không phụ thuộc vào root cho công việc hằng ngày.
- Hiểu cách VPC routing, security group, bastion access và endpoint phối hợp với nhau.
- Khởi chạy EC2 và triển khai thành công môi trường ứng dụng cơ bản.
Rèn luyện cách xử lý sự cố và dọn dẹp tài nguyên có hệ thống.




