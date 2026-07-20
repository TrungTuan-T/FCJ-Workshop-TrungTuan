---
title: "Blog 3"
date: 2026
weight: 1
chapter: false
pre: " <b> 3.3. </b> "
---


# Amazon DynamoDB - Chọn Chế Độ Throughput Phù Hợp Cho Ứng Dụng

## Giới thiệu

Trong quá trình phát triển ứng dụng trên AWS, việc lựa chọn cơ sở dữ liệu phù hợp ảnh hưởng trực tiếp đến hiệu năng, khả năng mở rộng và chi phí vận hành. Với những ứng dụng có lưu lượng lớn hoặc thay đổi liên tục như thương mại điện tử, game online hay hệ thống serverless, Amazon DynamoDB là một lựa chọn phổ biến.

DynamoDB là dịch vụ NoSQL được AWS quản lý hoàn toàn. Người dùng không cần cài đặt máy chủ, bảo trì hay mở rộng hạ tầng. Tuy nhiên, người phát triển cần hiểu rõ cách DynamoDB xử lý throughput để chọn chế độ phù hợp.



---

## Capacity Mode trong DynamoDB

Khi tạo bảng DynamoDB, người dùng cần chọn Capacity Mode. Đây là cơ chế quản lý tài nguyên để xử lý yêu cầu đọc và ghi.

DynamoDB cung cấp hai chế độ chính:

On-Demand Capacity
Provisioned Capacity
Hai chế độ này đều phục vụ mục tiêu xử lý dữ liệu nhanh chóng nhưng khác nhau về cách hoạt động và mô hình tính phí.

---

## On-Demand Capacity

On-Demand Capacity là chế độ trong đó AWS tự động quản lý khả năng xử lý của bảng DynamoDB. Người dùng không cần khai báo trước số lượng Read Capacity Unit (RCU) hay Write Capacity Unit (WCU). DynamoDB sẽ tự mở rộng hoặc thu hẹp theo lưu lượng thực tế.

Chế độ này phù hợp với ứng dụng mới phát triển hoặc có lưu lượng khó dự đoán. Ví dụ, một startup vừa ra mắt ứng dụng đặt đồ ăn sẽ khó biết chính xác số lượng người dùng trong những ngày đầu. On-Demand giúp hệ thống đáp ứng khi traffic tăng mà không cần cấu hình trước.

---

## Ưu điểm

Dễ triển khai, không cần tính toán RCU/WCU.
Tự động mở rộng theo nhu cầu thực tế.
Chỉ trả tiền theo số request đọc và ghi thực tế.
Phù hợp với kiến trúc serverless, Lambda và API Gateway.

---

## Hạn chế

Nếu hệ thống luôn có lưu lượng lớn và ổn định, On-Demand có thể đắt hơn Provisioned Capacity. Ngoài ra, vì chi phí phụ thuộc vào request thực tế nên ngân sách có thể khó dự đoán hơn.

---

## Khi nào nên dùng On-Demand?

On-Demand phù hợp cho ứng dụng mới, hệ thống đang thử nghiệm, startup chưa có dữ liệu dự báo, website flash sale, game online có traffic theo sự kiện và các hệ thống serverless.

---

## Provisioned Capacity

Provisioned Capacity là chế độ người dùng khai báo trước lượng tài nguyên mà bảng DynamoDB sẽ sử dụng.

Read Capacity Unit (RCU): đơn vị đo khả năng đọc dữ liệu.
Write Capacity Unit (WCU): đơn vị đo khả năng ghi dữ liệu.
Ví dụ, nếu một website xử lý 500 đơn hàng mỗi giây và mỗi đơn hàng khoảng 1 KB, bảng cần tối thiểu 500 WCU để ghi dữ liệu ổn định.

---

## Auto Scaling

Provisioned Capacity có thể kết hợp với Auto Scaling. Khi mức sử dụng vượt ngưỡng như 70%, DynamoDB có thể tự tăng RCU/WCU. Khi lưu lượng giảm, hệ thống tự giảm capacity để tiết kiệm chi phí.

---

## Ưu điểm

Tiết kiệm chi phí nếu lưu lượng ổn định.
Chủ động kiểm soát tài nguyên.
Phù hợp với hệ thống lớn như ERP, CRM, quản lý kho hoặc API nội bộ.

---

## Hạn chế

Nếu cấu hình quá thấp, DynamoDB có thể bị throttling. Nếu cấu hình quá cao nhưng sử dụng ít, doanh nghiệp vẫn phải trả tiền cho phần capacity không dùng đến. Việc theo dõi CloudWatch và điều chỉnh capacity cũng đòi hỏi kinh nghiệm vận hành.

---

## Warm Throughput

Warm Throughput giúp DynamoDB chuẩn bị sẵn năng lực xử lý cho các đợt tăng lưu lượng lớn. Tính năng này hữu ích cho flash sale, hệ thống bán vé, game online ra mắt phiên bản mới hoặc chiến dịch marketing có thời điểm truy cập cao.

---

## Throttling và Hot Partition

Throttling xảy ra khi lượng request vượt quá khả năng xử lý hiện tại của bảng hoặc từng partition. Ví dụ, bảng chỉ có 500 WCU nhưng ứng dụng gửi 2.000 yêu cầu ghi mỗi giây, DynamoDB sẽ giới hạn tốc độ xử lý.

Hot Partition xảy ra khi phần lớn request tập trung vào một partition key duy nhất. Để tránh tình trạng này, cần thiết kế partition key sao cho dữ liệu được phân bố đều.

---

## Nên chọn Capacity Mode nào?

Nên chọn On-Demand Capacity khi ứng dụng mới phát triển, chưa dự đoán được người dùng, có traffic đột biến hoặc xây dựng theo kiến trúc serverless.

Nên chọn Provisioned Capacity khi hệ thống đã ổn định, có dữ liệu lịch sử để dự đoán lưu lượng, muốn tối ưu chi phí dài hạn và có đội ngũ vận hành theo dõi capacity.

Trong thực tế, nhiều doanh nghiệp bắt đầu với On-Demand để giảm công sức quản trị. Khi ứng dụng phát triển và lưu lượng ổn định hơn, họ chuyển sang Provisioned Capacity để tiết kiệm chi phí.

---

## Kết luận

Throughput là yếu tố quan trọng khi làm việc với DynamoDB vì nó ảnh hưởng đến hiệu năng, khả năng mở rộng và chi phí. On-Demand đơn giản và linh hoạt, còn Provisioned giúp kiểm soát chi phí tốt hơn với workload ổn định.

Bên cạnh Capacity Mode, các khái niệm như Warm Throughput, Throttling và Hot Partition cũng cho thấy rằng thiết kế partition key, theo dõi CloudWatch và sử dụng Auto Scaling đúng cách là điều cần thiết để hệ thống DynamoDB vận hành hiệu quả.