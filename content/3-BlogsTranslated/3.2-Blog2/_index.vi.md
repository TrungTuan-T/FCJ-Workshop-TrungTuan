---
title: "Blog 2"
date: 2026
weight: 1
chapter: false
pre: " <b> 3.2. </b> "
---


# Hệ Thống Tìm Kiếm Dữ Liệu JSON Hợp Nhất Trên AWS

**Giới thiệu**
Trong các ứng dụng hiện đại như nền tảng streaming, thương mại điện tử hoặc hệ thống dữ liệu lớn, dữ liệu thường không nằm trong một cơ sở dữ liệu duy nhất. Một phần dữ liệu cần truy xuất rất nhanh, một phần cần đảm bảo giao dịch, một phần khác lại phục vụ phân tích hoặc lưu trữ lâu dài.

Điều này đặt ra câu hỏi: làm thế nào để người dùng có thể tìm kiếm dữ liệu từ nhiều nguồn khác nhau một cách nhanh chóng và chính xác?

AWS cung cấp một hướng tiếp cận hiệu quả bằng cách kết hợp nhiều dịch vụ lưu trữ dữ liệu với Amazon OpenSearch Service để xây dựng một hệ thống tìm kiếm hợp nhất.

---

## Vì sao không sử dụng một cơ sở dữ liệu duy nhất?

Khi mới bắt đầu học lập trình và xây dựng website, tôi từng nghĩ rằng chỉ cần một cơ sở dữ liệu như MySQL là đủ để lưu trữ toàn bộ dữ liệu của hệ thống. Tuy nhiên, khi tìm hiểu sâu hơn về AWS và kiến trúc của các hệ thống lớn, tôi nhận ra cách tiếp cận đó chỉ phù hợp với dự án nhỏ.

Khi dữ liệu tăng lên hàng triệu hoặc hàng tỷ bản ghi, việc dùng một cơ sở dữ liệu duy nhất sẽ tạo ra nhiều giới hạn về hiệu năng, khả năng mở rộng và chi phí vận hành. Mỗi loại dữ liệu cần một công cụ phù hợp:

DynamoDB phù hợp với dữ liệu cần truy xuất nhanh.
Aurora PostgreSQL phù hợp với dữ liệu giao dịch.
DocumentDB phù hợp với dữ liệu JSON linh hoạt.
S3 phù hợp với lưu trữ dữ liệu lớn.
Redshift phù hợp với phân tích dữ liệu.
Nếu chỉ sử dụng một hệ thống duy nhất, hiệu năng và khả năng mở rộng sẽ bị hạn chế.

---


## Kiến trúc tổng thể

Hệ thống gồm hai phần chính.

Tầng lưu trữ dữ liệu gồm Amazon DynamoDB, Amazon Aurora PostgreSQL, Amazon DocumentDB, Amazon S3 và Amazon Redshift. Mỗi dịch vụ lưu trữ một loại dữ liệu riêng theo đặc điểm nghiệp vụ.

Tầng tìm kiếm sử dụng Amazon OpenSearch Service. Dữ liệu cần tìm kiếm được đồng bộ vào OpenSearch để người dùng chỉ cần gửi truy vấn đến một nơi thay vì tìm kiếm trên từng cơ sở dữ liệu riêng lẻ.                                |

---

## Vai trò của các dịch vụ AWS

Amazon DynamoDB - xử lý dữ liệu thời gian thực
Amazon DynamoDB là cơ sở dữ liệu NoSQL có độ trễ thấp và khả năng mở rộng cao. Dịch vụ này phù hợp cho hồ sơ người dùng, lịch sử hoạt động và dữ liệu thời gian thực.

Ví dụ, trên nền tảng xem phim, thông tin như user_id, tên phim và thời điểm đang xem có thể được lưu trong DynamoDB. Khi người dùng tiếp tục xem phim trên thiết bị khác, DynamoDB có thể trả dữ liệu gần như ngay lập tức.

---

## Amazon Aurora PostgreSQL

Aurora PostgreSQL phù hợp với dữ liệu giao dịch như thanh toán, đơn hàng và thông tin giao dịch. Nhờ hỗ trợ ACID Transaction, Aurora giúp đảm bảo dữ liệu luôn nhất quán và đáng tin cậy ngay cả khi hệ thống gặp sự cố.

---

## Amazon DocumentDB

- DocumentDB phù hợp với dữ liệu JSON linh hoạt như thông tin sản phẩm, metadata hoặc nội dung không có schema cố định. Với thương mại điện tử, mỗi sản phẩm có thể có các thuộc tính khác nhau mà không cần thay đổi cấu trúc cơ sở dữ liệu.
---

## Amazon S3

Amazon S3 được sử dụng làm data lake, nơi lưu trữ log, backup và dữ liệu lớn với chi phí thấp. S3 có khả năng mở rộng gần như không giới hạn và thường đóng vai trò nền tảng cho các pipeline phân tích dữ liệu.
---

## Amazon Redshift

Amazon Redshift cung cấp khả năng phân tích dữ liệu lớn hiệu suất cao. Với kiểu dữ liệu SUPER và PartiQL, Redshift có thể xử lý dữ liệu JSON phức tạp, phân cấp và phục vụ báo cáo lịch sử.

Trong ví dụ nền tảng phát trực tuyến phim, Redshift có thể phân tích hàng tỷ sự kiện như play, pause và skip từ S3 để tính điểm xu hướng, sau đó xuất kết quả tổng hợp để đưa vào OpenSearch nhằm cải thiện xếp hạng tìm kiếm.

---

## Amazon OpenSearch Service

OpenSearch là thành phần trung tâm của hệ thống tìm kiếm. Dịch vụ này hỗ trợ full-text search, fuzzy search, auto suggestion, vector search và AI search.

Ví dụ, khi người dùng nhập “Tìm điện thoại Samsung dưới 10 triệu”, OpenSearch có thể trả về các sản phẩm phù hợp ngay cả khi từ khóa chưa hoàn toàn chính xác.

---

### Đồng bộ dữ liệu vào OpenSearch

AWS cung cấp OpenSearch Ingestion (OSI) để thu thập, chuyển đổi và đồng bộ dữ liệu vào OpenSearch. Quá trình này thường gồm hai giai đoạn:

Initial Load: nạp toàn bộ dữ liệu ban đầu từ DynamoDB, Aurora, DocumentDB hoặc S3 vào OpenSearch.
Change Data Capture: sau khi có baseline dữ liệu, hệ thống chỉ đồng bộ các thay đổi mới nhất.
Cách tiếp cận này giúp giảm tải cho hệ thống nguồn, giảm chi phí và giữ dữ liệu gần thời gian thực.

### Kết luận

Việc kết hợp DynamoDB, Aurora, DocumentDB, S3, Redshift và OpenSearch giúp xây dựng một hệ thống dữ liệu hiện đại, linh hoạt và dễ mở rộng. Amazon OpenSearch Service đóng vai trò trung tâm trong việc hợp nhất dữ liệu và cung cấp khả năng tìm kiếm mạnh mẽ cho người dùng.

Đây là kiến trúc phổ biến trong các hệ thống quy mô lớn như nền tảng streaming, thương mại điện tử và các ứng dụng dữ liệu hiện đại trên AWS.

**Nguồn tham khảo:** <https://awsstudygroup.com/2026/05/20/cach-xay-dung-giai-phap-tim-kiem-json-hop-nhat-trong-aws/>
