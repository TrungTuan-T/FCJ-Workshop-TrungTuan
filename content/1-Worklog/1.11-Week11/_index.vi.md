---
title: "Worklog Tuần 11"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 1.11. </b> "
---


# Worklog Tuần 11

## Mục tiêu tuần 11

Trong tuần 11, mục tiêu chính là tìm hiểu mô hình **Serverless trên AWS** và các dịch vụ thường được sử dụng để xây dựng backend không cần quản lý máy chủ. Nội dung này thuộc nhóm **Modernize / Hiện đại hóa ứng dụng** trong AWS Cloud Journey, tập trung vào serverless computing, API-first, hệ thống hướng sự kiện, xác thực người dùng và messaging bất đồng bộ. ([Cloud Journey][1])

Các nội dung trọng tâm của tuần bao gồm:

* Tìm hiểu tổng quan về kiến trúc serverless.
* Tìm hiểu **AWS Lambda** để xử lý logic backend.
* Tìm hiểu **Amazon API Gateway** để tạo REST API.
* Tìm hiểu **Amazon Cognito** để xác thực người dùng.
* Tìm hiểu **Amazon SQS** và **Amazon SNS** để xử lý hàng đợi và thông báo.
* Tìm hiểu **Amazon SES** để gửi email tự động trong ứng dụng.
* Tổng hợp mô hình kết hợp Lambda, API Gateway, Cognito, SQS, SNS và SES.

---

## Các công việc cần triển khai trong tuần này

| Thứ tự | Công việc thực hiện                                                                                                                                | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                                                                                                                                                                                                                                                                                                                                                                                                       |
| ------ | -------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | --------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **1**  | Tìm hiểu tổng quan về hiện đại hóa ứng dụng trên AWS, mô hình serverless, API-first, microservices và event-driven architecture.                   | 31/05/2026   | 31/05/2026      | [https://cloudjourney.awsstudygroup.com/vi/4-modernize/](https://cloudjourney.awsstudygroup.com/vi/4-modernize/) <br> [https://000078.awsstudygroup.com/vi/](https://000078.awsstudygroup.com/vi/)                                                                                                                                                                                                                   |
| **2**  | Tìm hiểu AWS Lambda, cách tạo Lambda Function, runtime, handler, trigger, IAM Role và cách Lambda xử lý logic backend mà không cần quản lý server. | 1/06/2026   | 1/06/2026      | [https://000078.awsstudygroup.com/vi/](https://000078.awsstudygroup.com/vi/) <br> [https://000078.awsstudygroup.com/vi/2-xu-ly-va-toi-uu-kich-thuoc-anh-tren-aws/](https://000078.awsstudygroup.com/vi/2-xu-ly-va-toi-uu-kich-thuoc-anh-tren-aws/) <br> [https://000066.awsstudygroup.com/](https://000066.awsstudygroup.com/)                                                                                       |
| **3**  | Tìm hiểu Amazon API Gateway, cách tạo API, method, resource, tích hợp API Gateway với Lambda, bật CORS và kiểm tra API bằng Postman hoặc frontend. | 2/06/2026   | 2/06/2026      | [https://000079.awsstudygroup.com/vi/](https://000079.awsstudygroup.com/vi/) <br> [https://000079.awsstudygroup.com/vi/4-thiet-lap-api-gateway/](https://000079.awsstudygroup.com/vi/4-thiet-lap-api-gateway/) <br> [https://000066.awsstudygroup.com/](https://000066.awsstudygroup.com/)                                                                                                                           |
| **4**  | Tìm hiểu Amazon Cognito, User Pool, Identity Pool, luồng đăng ký, đăng nhập, xác thực token và tích hợp Cognito với API/Lambda.                    | 3/06/2026   | 23/06/2026      | [https://000081.awsstudygroup.com/vi/](https://000081.awsstudygroup.com/vi/) <br> [https://000081.awsstudygroup.com/vi/2-create-user-pool/](https://000081.awsstudygroup.com/vi/2-create-user-pool/) <br> [https://000081.awsstudygroup.com/vi/3-create-api-and-lambda-function/](https://000081.awsstudygroup.com/vi/3-create-api-and-lambda-function/)                                                             |
| **5**  | Tìm hiểu SQS, SNS và SES; cách xử lý hàng đợi, gửi thông báo, gửi email tự động và tổng hợp luồng serverless hoàn chỉnh.                           | 4/06/2026   | 4/06/2026      | [https://000083.awsstudygroup.com/vi/](https://000083.awsstudygroup.com/vi/) <br> [https://000083.awsstudygroup.com/vi/2-create-queue-and-sns-topic/](https://000083.awsstudygroup.com/vi/2-create-queue-and-sns-topic/) <br> [https://aws.amazon.com/vi/ses/](https://aws.amazon.com/vi/ses/) <br> [https://docs.aws.amazon.com/ses/latest/dg/Welcome.html](https://docs.aws.amazon.com/ses/latest/dg/Welcome.html) |

---

## Kết quả đạt được tuần 11

### Tổng quan

Trong tuần này, tôi đã tìm hiểu mô hình **serverless application** trên AWS. Một kiến trúc serverless cơ bản có thể sử dụng **AWS Lambda** để xử lý logic, **Amazon API Gateway** để nhận request từ người dùng, **Amazon Cognito** để xác thực, **Amazon DynamoDB/S3** để lưu trữ dữ liệu và các dịch vụ messaging như **SQS/SNS** để xử lý bất đồng bộ. AWS Study Group cũng mô tả backend serverless có thể dùng Lambda, API Gateway, S3, DynamoDB và Cognito để xây dựng ứng dụng hoàn chỉnh. ([000066.awsstudygroup.com][2])

### Kiến thức đã học

Sau khi hoàn thành tuần 11, tôi đã nắm được:

* Hiểu được **serverless** là mô hình triển khai ứng dụng trong đó lập trình viên tập trung vào code và logic nghiệp vụ, hạn chế việc quản lý máy chủ.
* Hiểu **AWS Lambda** dùng để chạy code backend theo sự kiện, có thể kết hợp với S3, DynamoDB, API Gateway hoặc các dịch vụ khác. Workshop Lambda với S3 và DynamoDB cho thấy Lambda có thể xử lý logic, tương tác với dữ liệu và được kích hoạt bởi sự kiện. ([000078.awsstudygroup.com][3])
* Hiểu **Amazon API Gateway** dùng để tạo API cho frontend hoặc client gọi đến backend Lambda. Trong workshop API Gateway, frontend tương tác với cơ sở dữ liệu thông qua Lambda và API Gateway. ([000079.awsstudygroup.com][4])
* Hiểu **Amazon Cognito** hỗ trợ đăng ký, đăng nhập, xác thực, quản lý người dùng, User Pool và Identity Pool cho ứng dụng web hoặc mobile. ([000081.awsstudygroup.com][5])
* Hiểu **Amazon SQS** dùng để tạo hàng đợi tin nhắn, giúp tách rời các thành phần trong hệ thống và xử lý tác vụ bất đồng bộ.
* Hiểu **Amazon SNS** dùng để gửi thông báo theo mô hình publish/subscribe, trong đó publisher gửi message đến topic và subscriber nhận message từ topic. ([000083.awsstudygroup.com][6])
* Hiểu **Amazon SES** là dịch vụ email trên cloud, có thể tích hợp vào ứng dụng để gửi email giao dịch, email thông báo hoặc email tự động. ([Amazon Web Services, Inc.][7])

---

## Thực hành

Trong quá trình học và tìm hiểu, tôi đã thực hiện được các nội dung sau:

* Tìm hiểu nhóm lộ trình **Hiện đại hóa ứng dụng** trên AWS Cloud Journey.
* Tìm hiểu kiến trúc serverless gồm frontend, API Gateway, Lambda, Cognito và database.
* Tìm hiểu cách tạo Lambda Function và cấu hình quyền IAM Role cho Lambda.
* Tìm hiểu cách Lambda tương tác với S3 và DynamoDB.
* Tìm hiểu cách tạo API bằng Amazon API Gateway.
* Tìm hiểu cách cấu hình method, resource, CORS và tích hợp API Gateway với Lambda.
* Tìm hiểu cách tạo User Pool trong Amazon Cognito.
* Tìm hiểu luồng đăng ký, đăng nhập và xác thực token bằng Cognito.
* Tìm hiểu cách tạo SQS Queue và SNS Topic.
* Tìm hiểu cách gửi thông báo khi có sự kiện trong hệ thống.
* Tìm hiểu Amazon SES và trường hợp sử dụng để gửi email xác nhận, email thông báo hoặc email giao dịch.
* Ghi chú các lỗi thường gặp như thiếu quyền IAM cho Lambda, lỗi CORS khi gọi API, sai endpoint API Gateway, token Cognito không hợp lệ, Lambda timeout hoặc chưa dọn dẹp tài nguyên sau khi thực hành.

---

## Bảng tổng hợp dịch vụ tuần 11

| Dịch vụ                | Mục đích chính                     | Vai trò trong mô hình serverless              |
| ---------------------- | ---------------------------------- | --------------------------------------------- |
| **AWS Lambda**         | Chạy code không cần quản lý server | Xử lý logic backend                           |
| **Amazon API Gateway** | Tạo và quản lý API                 | Nhận request từ frontend/client và gọi Lambda |
| **Amazon Cognito**     | Xác thực và quản lý người dùng     | Đăng ký, đăng nhập, cấp token                 |
| **Amazon SQS**         | Hàng đợi tin nhắn                  | Xử lý tác vụ bất đồng bộ, tách rời hệ thống   |
| **Amazon SNS**         | Gửi thông báo theo topic           | Gửi notification đến subscriber               |
| **Amazon SES**         | Gửi email                          | Gửi email xác nhận, thông báo, giao dịch      |

---

## Tóm tắt tuần 11

**Tuần 11:** Tìm hiểu Lambda, API Gateway, Cognito, SQS, SNS và SES trong mô hình serverless. Nắm được cách xây dựng backend không cần quản lý máy chủ, tạo API cho frontend, xác thực người dùng, xử lý tác vụ bất đồng bộ và gửi thông báo/email trong hệ thống AWS.

[1]: https://cloudjourney.awsstudygroup.com/vi/4-modernize/ "Hiện đại hóa ứng dụng :: Hành trình đầu tiên lên Mây"
[2]: https://000066.awsstudygroup.com/ "Serverless with Lambda, API Gateway and SAM :: SERVERLESS WITH LAMBDA, API GATEWAY AND SAM"
[3]: https://000078.awsstudygroup.com/vi/ "Serverless - Tương tác giữa Lambda với S3 và DynamoDB :: AWS System Manager"
[4]: https://000079.awsstudygroup.com/vi/ "Serverless - Hướng dẫn viết Front-end gọi API Gateway :: Serverless - Build Frontend to call API Gateway"
[5]: https://000081.awsstudygroup.com/vi/ "Serverless - Xác thực với Amazon Cognito :: SERVERLESS - XÁC THỰC VỚI AMAZON COGNITO"
[6]: https://000083.awsstudygroup.com/vi/ "Serverless - Xử lý đơn hàng với SQS-SNS :: SERVERLESS - XỬ LÝ ĐƠN HÀNG VỚI SQS-SNS"
[7]: https://aws.amazon.com/vi/ses/?utm_source=chatgpt.com "Amazon Simple Email Service (Amazon SES)"
