---
title: "Worklog Tuần 10"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 1.10. </b> "
---


# Worklog Tuần 10

## Mục tiêu tuần 10

Trong tuần 10, mục tiêu chính là tìm hiểu nhóm nội dung **tối ưu chi phí và bảo mật hệ thống trên AWS**. Đây là phần thuộc nhóm **Optimize / Tối ưu hệ thống** trong AWS Cloud Journey, tập trung vào các nội dung như bảo mật, độ tin cậy, hiệu năng, tối ưu chi phí và vận hành hệ thống sau khi triển khai lên AWS. ([Cloud Journey][1])

Các nội dung trọng tâm của tuần bao gồm:

* Tìm hiểu tổng quan về tối ưu chi phí trên AWS.
* Tìm hiểu **AWS Budgets** để tạo ngân sách và cảnh báo chi phí.
* Tìm hiểu **AWS Cost Explorer** để phân tích chi phí sử dụng dịch vụ.
* Tìm hiểu **AWS KMS** để quản lý khóa và mã hóa dữ liệu.
* Tìm hiểu **AWS WAF** để bảo vệ ứng dụng web và API.
* Tìm hiểu thêm **AWS Security Hub** để kiểm tra, đánh giá tiêu chuẩn bảo mật.
* Ghi chú các best practices về bảo mật và quản lý chi phí trên AWS.

---

## Các công việc cần triển khai trong tuần này

| Thứ tự | Công việc thực hiện                                                                                                                                                                                   | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| --- | --- | --- | --- | --- |
| **1**  | Tìm hiểu tổng quan về nhóm Optimize trên AWS, bao gồm tối ưu chi phí, bảo mật, độ tin cậy, hiệu năng và vận hành hệ thống.                                                                            | 26/05/2026 | 26/05/2026 | [https://cloudjourney.awsstudygroup.com/vi/3-optimize/](https://cloudjourney.awsstudygroup.com/vi/3-optimize/)                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| **2**  | Tìm hiểu AWS Budgets và Cost Explorer, cách tạo ngân sách, theo dõi chi phí, phân tích chi phí theo dịch vụ và thiết lập cảnh báo khi vượt ngưỡng.                                                    | 27/05/2026 | 27/05/2026 | [https://000001.awsstudygroup.com/vi/7-monitoring-v%C3%A0-t%E1%BB%91i-%C6%B0u-chi-ph%C3%AD/](https://000001.awsstudygroup.com/vi/7-monitoring-v%C3%A0-t%E1%BB%91i-%C6%B0u-chi-ph%C3%AD/) <br> [https://docs.aws.amazon.com/cost-management/latest/userguide/budgets-managing-costs.html](https://docs.aws.amazon.com/cost-management/latest/userguide/budgets-managing-costs.html) <br> [https://docs.aws.amazon.com/cost-management/latest/userguide/ce-what-is.html](https://docs.aws.amazon.com/cost-management/latest/userguide/ce-what-is.html) |
| **3**  | Tìm hiểu AWS KMS, vai trò của Key Management Service trong việc tạo, quản lý khóa mã hóa và bảo vệ dữ liệu trong các dịch vụ AWS.                                                                     | 28/05/2026 | 27/05/2026 | [https://000033.awsstudygroup.com/vi/](https://000033.awsstudygroup.com/vi/) <br> [https://docs.aws.amazon.com/kms/](https://docs.aws.amazon.com/kms/)                                                                                                                                                                                                                                                                                                                                                                                               |
| **4**  | Tìm hiểu AWS WAF, cách Web Application Firewall hỗ trợ bảo vệ website, API và ứng dụng khỏi các dạng tấn công phổ biến như SQL Injection, XSS hoặc bot traffic.                                       | 29/05/2026 | 29/05/2026 | [https://000026.awsstudygroup.com/vi/](https://000026.awsstudygroup.com/vi/) <br> [https://aws.amazon.com/waf/](https://aws.amazon.com/waf/) <br> [https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-control-access-aws-waf.html](https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-control-access-aws-waf.html)                                                                                                                                                                                         |
| **5**  | Tìm hiểu AWS Security Hub, tổng hợp kiến thức về bảo mật và tối ưu chi phí; ghi chú các nguyên tắc như theo dõi chi phí thường xuyên, giới hạn quyền truy cập, mã hóa dữ liệu và bảo vệ ứng dụng web. | 30/05/2026 | 30/05/2026 | [https://000018.awsstudygroup.com/vi/](https://000018.awsstudygroup.com/vi/) <br> [https://docs.aws.amazon.com/securityhub/latest/userguide/what-is-securityhub.html](https://docs.aws.amazon.com/securityhub/latest/userguide/what-is-securityhub.html) <br> [https://cloudjourney.awsstudygroup.com/vi/3-optimize/](https://cloudjourney.awsstudygroup.com/vi/3-optimize/)                                                                                                                                                                         |

---

## Kết quả đạt được tuần 10

### Tổng quan

Trong tuần này, tôi đã tìm hiểu các dịch vụ hỗ trợ **tối ưu chi phí và tăng cường bảo mật hệ thống trên AWS**. Nội dung gồm hai nhóm chính: nhóm quản lý chi phí với **AWS Budgets** và **AWS Cost Explorer**, nhóm bảo mật với **AWS KMS**, **AWS WAF** và **AWS Security Hub**. AWS Budgets hỗ trợ theo dõi chi phí và mức sử dụng theo ngân sách, còn Cost Explorer hỗ trợ xem và phân tích chi phí sử dụng AWS. ([AWS Documentation][2])

### Kiến thức đã học

Sau khi hoàn thành tuần 10, tôi đã nắm được:

* Hiểu được vai trò của tối ưu chi phí trong quá trình triển khai hệ thống trên AWS.
* Biết cách sử dụng **AWS Budgets** để tạo ngân sách, đặt ngưỡng cảnh báo và theo dõi chi phí sử dụng.
* Biết cách sử dụng **AWS Cost Explorer** để phân tích chi phí theo dịch vụ, thời gian, tài khoản hoặc tag.
* Hiểu **AWS KMS** là dịch vụ quản lý khóa và mã hóa, được dùng để bảo vệ dữ liệu trong các dịch vụ AWS hoặc trong ứng dụng riêng. ([AWS Documentation][3])
* Hiểu **AWS WAF** là Web Application Firewall dùng để bảo vệ website, API và ứng dụng khỏi bot, exploit và các dạng tấn công tầng ứng dụng như SQL Injection hoặc Cross-site Scripting. ([Amazon Web Services, Inc.][4])
* Hiểu **AWS Security Hub** hỗ trợ kiểm tra các security controls và tạo findings để đánh giá mức độ tuân thủ theo best practices bảo mật. ([AWS Documentation][5])
* Nhận biết tầm quan trọng của việc kết hợp giữa theo dõi chi phí, phân quyền IAM hợp lý, mã hóa dữ liệu và bảo vệ ứng dụng bằng WAF.

---

## Thực hành

Trong quá trình học và tìm hiểu, tôi đã thực hiện được các nội dung sau:

* Truy cập AWS Billing and Cost Management trên AWS Console.
* Tìm hiểu cách xem chi phí sử dụng dịch vụ AWS.
* Tìm hiểu cách tạo budget để theo dõi chi phí hằng tháng.
* Tìm hiểu cách cấu hình cảnh báo khi chi phí vượt ngưỡng.
* Tìm hiểu cách xem biểu đồ chi phí bằng Cost Explorer.
* Tìm hiểu AWS KMS, Customer Managed Key và AWS Managed Key.
* Tìm hiểu cách AWS KMS hỗ trợ mã hóa dữ liệu cho các dịch vụ như S3, EBS hoặc RDS.
* Tìm hiểu AWS WAF, Web ACL, Rule và Managed Rule Group.
* Tìm hiểu cách AWS WAF có thể gắn với CloudFront, Application Load Balancer hoặc API Gateway.
* Tìm hiểu AWS Security Hub và vai trò của security findings trong việc đánh giá bảo mật.
* Ghi chú các lỗi thường gặp như chưa bật Cost Explorer, chưa tạo Budget, thiếu quyền IAM, cấu hình WAF sai rule hoặc chưa bật mã hóa cho dữ liệu quan trọng.

---

## Bảng tổng hợp dịch vụ tuần 10

| Dịch vụ               | Mục đích chính                    | Vai trò trong hệ thống                                         |
| --------------------- | --------------------------------- | -------------------------------------------------------------- |
| **AWS Budgets**       | Tạo ngân sách và cảnh báo chi phí | Giúp kiểm soát chi phí, tránh phát sinh vượt mức               |
| **AWS Cost Explorer** | Phân tích chi phí sử dụng AWS     | Giúp xem dịch vụ nào đang tiêu tốn nhiều chi phí               |
| **AWS KMS**           | Quản lý khóa và mã hóa dữ liệu    | Bảo vệ dữ liệu trong quá trình lưu trữ và sử dụng              |
| **AWS WAF**           | Bảo vệ website và API             | Chặn request độc hại, bot traffic, SQL Injection, XSS          |
| **AWS Security Hub**  | Đánh giá trạng thái bảo mật       | Tổng hợp security findings và kiểm tra theo tiêu chuẩn bảo mật |

---

## Tóm tắt tuần 10

**Tuần 10:** Tìm hiểu AWS Budgets, Cost Explorer, KMS, WAF và tối ưu chi phí, bảo mật hệ thống. Nắm được cách theo dõi chi phí, tạo cảnh báo ngân sách, phân tích mức sử dụng dịch vụ, mã hóa dữ liệu, bảo vệ ứng dụng web/API và đánh giá trạng thái bảo mật trên AWS.

[1]: https://cloudjourney.awsstudygroup.com/vi/3-optimize/ "Tối ưu hệ thống :: Hành trình đầu tiên lên Mây"
[2]: https://docs.aws.amazon.com/cost-management/latest/userguide/budgets-managing-costs.html?utm_source=chatgpt.com "Managing your costs with AWS Budgets"
[3]: https://docs.aws.amazon.com/kms/?utm_source=chatgpt.com "AWS Key Management Service Documentation"
[4]: https://aws.amazon.com/waf/?utm_source=chatgpt.com "Web Application Firewall - Web API Protection - AWS WAF"
[5]: https://docs.aws.amazon.com/securityhub/latest/userguide/what-is-securityhub.html?utm_source=chatgpt.com "Introduction to AWS Security Hub CSPM"
