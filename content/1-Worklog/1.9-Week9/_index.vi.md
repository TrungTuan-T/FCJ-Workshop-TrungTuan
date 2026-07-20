---
title: "Worklog Tuần 9"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.9. </b> "
---


# Worklog Tuần 9

## Mục tiêu tuần 9

Trong tuần 9, mục tiêu chính là tìm hiểu các dịch vụ **giám sát, ghi log và theo dõi hoạt động hệ thống trên AWS**, bao gồm **Amazon CloudWatch** và **AWS CloudTrail**. Đây là các dịch vụ quan trọng giúp người quản trị theo dõi hiệu năng tài nguyên, phát hiện lỗi, tạo cảnh báo và kiểm tra lịch sử hoạt động trong tài khoản AWS.

Nội dung tuần này thuộc nhóm **Optimize / Tối ưu hệ thống trên AWS**, tập trung vào vận hành, giám sát, bảo mật, hiệu năng và tối ưu hệ thống sau khi triển khai lên AWS. ([Cloud Journey][1])

Các nội dung trọng tâm của tuần bao gồm:

* Tìm hiểu tổng quan về giám sát hệ thống trên AWS.
* Tìm hiểu **Amazon CloudWatch Metrics**.
* Tìm hiểu **Amazon CloudWatch Logs** và **CloudWatch Logs Insights**.
* Tìm hiểu cách tạo **CloudWatch Alarm**.
* Tìm hiểu cách tạo **CloudWatch Dashboard**.
* Tìm hiểu **AWS CloudTrail** để ghi nhận lịch sử API call và hoạt động người dùng.
* Phân biệt vai trò của CloudWatch và CloudTrail trong quá trình vận hành hệ thống.

---

## Các công việc cần triển khai trong tuần này

| Thứ tự | Công việc thực hiện                                                                                                                                                 | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| --- | --- | --- | --- | --- |
| **1**  | Tìm hiểu tổng quan về giám sát hệ thống trên AWS, vai trò của CloudWatch và CloudTrail trong vận hành, troubleshooting và bảo mật hệ thống.                         | 22/05/2026 | 22/05/2026 | [https://cloudjourney.awsstudygroup.com/vi/3-optimize/](https://cloudjourney.awsstudygroup.com/vi/3-optimize/) <br> [https://000008.awsstudygroup.com/vi/](https://000008.awsstudygroup.com/vi/) <br> [https://docs.aws.amazon.com/decision-guides/latest/cloudtrail-or-cloudwatch/cloudtrail-or-cloudwatch.html](https://docs.aws.amazon.com/decision-guides/latest/cloudtrail-or-cloudwatch/cloudtrail-or-cloudwatch.html)                                                                                                                                                                                                           |
| **2**  | Tìm hiểu CloudWatch Metrics, cách xem metric của EC2, RDS, EBS hoặc các dịch vụ AWS khác; tìm hiểu namespace, dimension và statistic.                               | 22/05/2026 | 22/05/2026 | [https://000008.awsstudygroup.com/vi/3-cloud-watch-metric/](https://000008.awsstudygroup.com/vi/3-cloud-watch-metric/) <br> [https://000008.awsstudygroup.com/vi/3-cloud-watch-metric/3.1-viewing-metrics/](https://000008.awsstudygroup.com/vi/3-cloud-watch-metric/3.1-viewing-metrics/) <br> [https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/working_with_metrics.html](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/working_with_metrics.html)                                                                                                                                                   |
| **3**  | Tìm hiểu CloudWatch Logs, Log Group, Log Stream, Logs Insights và cách sử dụng log để hỗ trợ kiểm tra lỗi trong hệ thống.                                           | 23/05/2026 | 23/05/2026 | [https://000008.awsstudygroup.com/vi/4-cloud-watch-log/](https://000008.awsstudygroup.com/vi/4-cloud-watch-log/) <br> [https://000008.awsstudygroup.com/vi/4-cloud-watch-log/4.1-cloud-watch-logs/](https://000008.awsstudygroup.com/vi/4-cloud-watch-log/4.1-cloud-watch-logs/) <br> [https://000008.awsstudygroup.com/vi/4-cloud-watch-log/4.2-cloud-watch-logs-insights/](https://000008.awsstudygroup.com/vi/4-cloud-watch-log/4.2-cloud-watch-logs-insights/)                                                                                                                                                                     |
| **4**  | Tìm hiểu CloudWatch Alarm và CloudWatch Dashboard; thực hành tạo cảnh báo theo ngưỡng metric và tạo dashboard để quan sát tài nguyên.                               | 24/05/2026 | 24/05/2026 | [https://000008.awsstudygroup.com/vi/5-cloud-watch-alarm/](https://000008.awsstudygroup.com/vi/5-cloud-watch-alarm/) <br> [https://000008.awsstudygroup.com/vi/6-cloud-watch-dashboard/](https://000008.awsstudygroup.com/vi/6-cloud-watch-dashboard/) <br> [https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Alarms.html](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Alarms.html) <br> [https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Dashboards.html](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Dashboards.html) |
| **5**  | Tìm hiểu AWS CloudTrail, cách CloudTrail ghi lại hoạt động API trong tài khoản AWS; so sánh CloudWatch và CloudTrail, tổng hợp kiến thức và ghi chú lỗi thường gặp. | 25/05/2026 | 25/05/2026 | [https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-user-guide.html](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-user-guide.html) <br> [https://aws.amazon.com/cloudtrail/](https://aws.amazon.com/cloudtrail/) <br> [https://docs.aws.amazon.com/decision-guides/latest/cloudtrail-or-cloudwatch/cloudtrail-or-cloudwatch.html](https://docs.aws.amazon.com/decision-guides/latest/cloudtrail-or-cloudwatch/cloudtrail-or-cloudwatch.html) <br> [https://cloudjourney.awsstudygroup.com/vi/3-optimize/](https://cloudjourney.awsstudygroup.com/vi/3-optimize/)                           |

---

## Kết quả đạt được tuần 9

### Tổng quan

Trong tuần này, tôi đã tìm hiểu các dịch vụ phục vụ cho việc **giám sát và ghi nhận hoạt động hệ thống trên AWS**. Amazon CloudWatch được sử dụng để thu thập metric, log, tạo alarm và dashboard; trong khi đó AWS CloudTrail tập trung vào việc ghi lại hoạt động người dùng và API call trong tài khoản AWS. Theo tài liệu AWS, CloudWatch phục vụ theo dõi hiệu năng và trạng thái vận hành, còn CloudTrail phục vụ audit, bảo mật và kiểm tra lịch sử hoạt động. ([AWS Documentation][2])

### Kiến thức đã học

Sau khi hoàn thành tuần 9, tôi đã nắm được:

* Hiểu được vai trò của **monitoring** và **logging** trong quá trình vận hành hệ thống trên AWS.
* Hiểu **Amazon CloudWatch** dùng để theo dõi tài nguyên AWS và ứng dụng thông qua metric, log, alarm và dashboard.
* Hiểu **CloudWatch Metrics** là dữ liệu đo lường hiệu năng hệ thống, ví dụ như CPU Utilization, Network In/Out, Disk Read/Write hoặc các custom metric. AWS cho biết CloudWatch thu thập metric từ các dịch vụ như EC2, EBS, RDS và từ custom metric do người dùng gửi lên. ([AWS Documentation][3])
* Hiểu **CloudWatch Logs** dùng để lưu trữ, tìm kiếm và phân tích log trong hệ thống.
* Hiểu **CloudWatch Logs Insights** hỗ trợ truy vấn log để tìm lỗi hoặc phân tích sự kiện.
* Hiểu **CloudWatch Alarm** dùng để tạo cảnh báo khi metric vượt qua ngưỡng cấu hình.
* Hiểu **CloudWatch Dashboard** giúp hiển thị nhiều metric và alarm trên cùng một giao diện quan sát. ([AWS Documentation][4])
* Hiểu **AWS CloudTrail** dùng để ghi lại hoạt động trong tài khoản AWS, bao gồm các API call và hành động được thực hiện bởi user, role hoặc dịch vụ AWS. ([AWS Documentation][5])
* Phân biệt được CloudWatch dùng nhiều cho **monitoring hiệu năng**, còn CloudTrail dùng nhiều cho **audit và kiểm tra hoạt động bảo mật**.

---

## Thực hành

Trong quá trình học và tìm hiểu, tôi đã thực hiện được các nội dung sau:

* Truy cập dịch vụ Amazon CloudWatch trên AWS Management Console.
* Xem các metric cơ bản của EC2 Instance.
* Tìm hiểu cách lọc metric theo namespace và dimension.
* Tìm hiểu Log Group và Log Stream trong CloudWatch Logs.
* Tìm hiểu cách sử dụng CloudWatch Logs Insights để truy vấn log.
* Tạo CloudWatch Alarm để cảnh báo khi một metric vượt quá ngưỡng.
* Tìm hiểu cách tạo CloudWatch Dashboard để quan sát nhiều chỉ số cùng lúc.
* Truy cập dịch vụ AWS CloudTrail để xem Event History.
* Tìm hiểu cách CloudTrail ghi lại các thao tác như tạo, sửa, xóa tài nguyên AWS.
* Ghi chú các lỗi thường gặp như không thấy log do chưa cấu hình agent, chưa có quyền IAM, chọn sai Region, metric chưa có dữ liệu hoặc alarm đặt ngưỡng chưa phù hợp.

---

## Bảng so sánh CloudWatch và CloudTrail

| Tiêu chí                   | Amazon CloudWatch                                     | AWS CloudTrail                                  |
| -------------------------- | ----------------------------------------------------- | ----------------------------------------------- |
| **Mục đích chính**         | Giám sát hiệu năng và trạng thái hệ thống             | Ghi lại lịch sử hoạt động và API call           |
| **Dữ liệu theo dõi**       | Metric, log, alarm, dashboard                         | Event, API call, user activity                  |
| **Dùng khi nào**           | Khi cần theo dõi CPU, RAM, lỗi ứng dụng, log hệ thống | Khi cần biết ai đã làm gì trong tài khoản AWS   |
| **Hỗ trợ troubleshooting** | Tìm lỗi hiệu năng, lỗi ứng dụng, lỗi tài nguyên       | Kiểm tra lịch sử thay đổi, hành động người dùng |
| **Ví dụ sử dụng**          | Cảnh báo CPU EC2 vượt 80%                             | Kiểm tra user nào đã xóa một tài nguyên AWS     |

---

## Tóm tắt tuần 9

**Tuần 9:** Tìm hiểu CloudWatch, CloudTrail, log, metric, alarm và giám sát hệ thống. Nắm được cách theo dõi hiệu năng tài nguyên AWS, kiểm tra log, tạo cảnh báo, xây dựng dashboard và sử dụng CloudTrail để kiểm tra lịch sử hoạt động trong tài khoản AWS.

[1]: https://cloudjourney.awsstudygroup.com/3-optimize/?utm_source=chatgpt.com "Optimizing the system - The First Cloud Journey"
[2]: https://docs.aws.amazon.com/decision-guides/latest/cloudtrail-or-cloudwatch/cloudtrail-or-cloudwatch.html?utm_source=chatgpt.com "AWS CloudTrail or Amazon CloudWatch?"
[3]: https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/working_with_metrics.html?utm_source=chatgpt.com "Metrics in Amazon CloudWatch"
[4]: https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Dashboards.html?utm_source=chatgpt.com "Using Amazon CloudWatch dashboards - AWS Documentation"
[5]: https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-user-guide.html?utm_source=chatgpt.com "What Is AWS CloudTrail? - AWS CloudTrail"
