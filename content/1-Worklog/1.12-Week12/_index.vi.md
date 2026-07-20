---
title: "Worklog Tuần 12"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 1.12. </b> "
---


# Worklog Tuần 12

## Mục tiêu tuần 12

Trong tuần 12, mục tiêu chính là áp dụng các kiến thức AWS đã học vào quá trình thực hiện dự án. Nội dung tập trung vào việc phân tích yêu cầu hệ thống, lựa chọn các dịch vụ AWS phù hợp, thiết kế kiến trúc tổng thể và triển khai các thành phần chính của dự án trên môi trường AWS.

Dự án sử dụng các dịch vụ như **Amazon S3, CloudFront, Cognito, API Gateway, Lambda, DynamoDB, SQS, SNS, SES và CloudWatch** để xây dựng hệ thống theo hướng serverless, có khả năng lưu trữ dữ liệu, xác thực người dùng, xử lý nghiệp vụ, gửi thông báo và giám sát hoạt động hệ thống.

---

## Các công việc cần triển khai trong tuần này

| Thứ tự | Công việc thực hiện                                                                                                                    | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                                                                                                                                                                                                                                   |
| ------ | -------------------------------------------------------------------------------------------------------------------------------------- | ------------ | --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **1**  | Phân tích yêu cầu dự án, xác định các chức năng chính của hệ thống và lựa chọn các dịch vụ AWS phù hợp để triển khai.                  | 05/06/2026   | 05/06/2026      | [https://cloudjourney.awsstudygroup.com/vi/1-explore/](https://cloudjourney.awsstudygroup.com/vi/1-explore/) <br> [https://cloudjourney.awsstudygroup.com/vi/4-modernize/](https://cloudjourney.awsstudygroup.com/vi/4-modernize/)               |
| **2**  | Thiết kế kiến trúc tổng thể của dự án, xác định luồng hoạt động giữa người dùng, frontend, API Gateway, Lambda và cơ sở dữ liệu.       | 06/06/2026   | 10/06/2026      | [https://000079.awsstudygroup.com/vi/](https://000079.awsstudygroup.com/vi/) <br> [https://000078.awsstudygroup.com/vi/](https://000078.awsstudygroup.com/vi/)                                                                                   |
| **3**  | Triển khai phần lưu trữ và xác thực người dùng, bao gồm lưu frontend/file trên S3, phân phối bằng CloudFront và xác thực bằng Cognito. | 11/06/2026   | 15/06/2026      | [https://000057.awsstudygroup.com/vi/](https://000057.awsstudygroup.com/vi/) <br> [https://000081.awsstudygroup.com/vi/](https://000081.awsstudygroup.com/vi/)                                                                                   |
| **4**  | Xây dựng backend serverless bằng API Gateway và Lambda, kết nối Lambda với DynamoDB để xử lý thêm, sửa, xóa và truy vấn dữ liệu.       | 16/06/2026   | 20/06/2026      | [https://000079.awsstudygroup.com/vi/](https://000079.awsstudygroup.com/vi/) <br> [https://000078.awsstudygroup.com/vi/](https://000078.awsstudygroup.com/vi/) <br> [https://000053.awsstudygroup.com/vi/](https://000053.awsstudygroup.com/vi/) |
| **5**  | Tích hợp các chức năng gửi thông báo/email bằng SQS, SNS, SES; theo dõi log và lỗi hệ thống bằng CloudWatch.                           | 20/06/2026   | 25/06/2026      | [https://000083.awsstudygroup.com/vi/](https://000083.awsstudygroup.com/vi/) <br> [https://aws.amazon.com/vi/ses/](https://aws.amazon.com/vi/ses/) <br> [https://000008.awsstudygroup.com/vi/](https://000008.awsstudygroup.com/vi/)             |

---

## Kết quả đạt được tuần 12

### Tổng quan

Trong tuần này, tôi đã bắt đầu áp dụng kiến thức AWS vào dự án thực tế. Thay vì chỉ tìm hiểu từng dịch vụ riêng lẻ, tôi tập trung vào việc kết hợp các dịch vụ AWS để xây dựng một hệ thống hoàn chỉnh. Các dịch vụ được lựa chọn theo đúng vai trò trong kiến trúc, bao gồm lưu trữ frontend, xác thực người dùng, xử lý API, lưu dữ liệu, gửi thông báo và giám sát hệ thống.

### Kiến thức đã áp dụng

Sau khi thực hiện tuần 12, tôi đã áp dụng được:

* Sử dụng **Amazon S3** để lưu trữ frontend và file của hệ thống.
* Sử dụng **Amazon CloudFront** để phân phối website, tăng tốc truy cập và cải thiện hiệu năng.
* Sử dụng **Amazon Cognito** để xác thực người dùng, hỗ trợ đăng ký, đăng nhập và cấp token.
* Sử dụng **Amazon API Gateway** để tạo REST API và làm điểm giao tiếp giữa frontend với backend.
* Sử dụng **AWS Lambda** để xử lý logic nghiệp vụ theo mô hình serverless.
* Sử dụng **Amazon DynamoDB** để lưu trữ dữ liệu chính của hệ thống.
* Sử dụng **Amazon SQS** để xử lý tác vụ bất đồng bộ.
* Sử dụng **Amazon SNS** và **Amazon SES** để gửi thông báo hoặc email cho người dùng.
* Sử dụng **Amazon CloudWatch** để theo dõi log, kiểm tra lỗi và hỗ trợ troubleshooting.

---

## Thực hành

Trong quá trình thực hiện dự án, tôi đã tiến hành:

* Xác định các chức năng chính của hệ thống.
* Lựa chọn dịch vụ AWS phù hợp với từng chức năng.
* Thiết kế sơ đồ kiến trúc tổng thể của dự án.
* Xác định luồng hoạt động từ người dùng đến frontend, API, backend và database.
* Tìm hiểu cách frontend gọi API thông qua API Gateway.
* Tìm hiểu cách Lambda xử lý request và thao tác với DynamoDB.
* Tìm hiểu cách Cognito xác thực người dùng trước khi gọi API.
* Tìm hiểu cách SQS hỗ trợ xử lý tác vụ gửi thông báo/email bất đồng bộ.
* Theo dõi log và lỗi bằng CloudWatch.
* Ghi chú các vấn đề cần kiểm tra như phân quyền IAM, lỗi CORS, lỗi API Gateway, lỗi Lambda timeout, lỗi kết nối DynamoDB và chi phí phát sinh.

---

## Bảng dịch vụ sử dụng trong dự án

| Dịch vụ                | Vai trò trong dự án                        |
| ---------------------- | ------------------------------------------ |
| **Amazon S3**          | Lưu frontend và file của hệ thống          |
| **Amazon CloudFront**  | Phân phối website, tăng tốc truy cập       |
| **Amazon Cognito**     | Xác thực người dùng, cấp token đăng nhập   |
| **Amazon API Gateway** | Tạo REST API cho frontend gọi backend      |
| **AWS Lambda**         | Xử lý logic nghiệp vụ                      |
| **Amazon DynamoDB**    | Lưu trữ dữ liệu chính của hệ thống         |
| **Amazon SQS**         | Xử lý tác vụ bất đồng bộ                   |
| **Amazon SNS**         | Gửi thông báo theo sự kiện                 |
| **Amazon SES**         | Gửi email thông báo cho người dùng         |
| **Amazon CloudWatch**  | Ghi log, theo dõi lỗi và giám sát hệ thống |

---

## Tóm tắt tuần 12

**Tuần 12:** Áp dụng kiến thức AWS vào dự án, phân tích yêu cầu hệ thống, thiết kế kiến trúc tổng thể, lựa chọn dịch vụ phù hợp và bắt đầu triển khai các thành phần chính như S3, CloudFront, Cognito, API Gateway, Lambda, DynamoDB, SQS, SNS, SES và CloudWatch.
