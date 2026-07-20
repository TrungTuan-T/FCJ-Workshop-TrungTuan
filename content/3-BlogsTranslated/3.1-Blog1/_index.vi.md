---
title: "Blog 1"
date: 2026
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

## Hiện đại hóa KYC với các giải pháp serverless của AWS và AI agent cho dịch vụ tài chính

Trong bối cảnh ngành tài chính yêu cầu tốc độ xử lý nhanh và tính bảo mật khắt khe, quy trình KYC truyền thống thường gặp trở ngại do thao tác thủ công, thời gian chờ đợi kéo dài và khả năng sai sót cao. Bài viết này trình bày cách các tổ chức tài chính có thể chuyển đổi quy trình này bằng kiến trúc Serverless kết hợp với AI Agents.

---
## Vai trò quan trọng của KYC

- Bảo vệ hệ thống tài chính: KYC thực hiện xác minh danh tính khách hàng và phát hiện gian lận để bảo vệ sự an toàn của hệ thống.

- Hỗ trợ tuân thủ quy định pháp lý: KYC đóng vai trò then chốt trong việc tuân thủ các quy định về chống rửa tiền (AML) và chống tài trợ khủng bố (CTF).

- Ngăn chặn gian lận: Quy trình này giúp phát hiện và ngăn chặn các hành vi đánh cắp danh tính cũng như sử dụng tài liệu giả mạo.

- Quản lý rủi ro: KYC hỗ trợ đánh giá hồ sơ khách hàng và giám sát các giao dịch tài chính để kiểm soát rủi ro.

- Xây dựng lòng tin: Việc thực hiện KYC tạo ra sự minh bạch, từ đó củng cố lòng tin của khách hàng đối với tổ chức tài chính.

- Sự phức tạp khi mở rộng: Việc tuân thủ KYC ngày càng trở nên khó khăn khi các tổ chức tài chính mở rộng sang nhiều sản phẩm, phân khúc khách hàng (bán lẻ, SME, doanh nghiệp) và khu vực địa lý khác nhau.

- Yêu cầu về khuôn khổ pháp lý: Hoạt động trên nhiều khu vực đòi hỏi phải tuân thủ các khuôn khổ quy định đa dạng như BSA, Đạo luật USA PATRIOT (Hoa Kỳ), AMLD (EU), cũng như các hướng dẫn từ FATF và MAS (Singapore).

---
## KYC truyền thống 

Các quy trình KYC truyền thống xác minh danh tính khách hàng, đánh giá rủi ro và giám sát rửa tiền. Chúng dựa vào việc thu thập tài liệu thủ công, kiểm tra danh tính trên nhiều cơ sở dữ liệu và đánh giá định kỳ. 
Mặc dù các quy trình đã được thiết lập này đã phục vụ ngành tài chính trong nhiều thập kỷ, nhưng chúng được thiết kế cho một kỷ nguyên khác với khối lượng giao dịch thấp hơn, các sản phẩm đơn giản hơn và bối cảnh mối đe dọa ít tinh vi hơn. Môi trường tài chính ưu tiên kỹ thuật số ngày nay đòi hỏi một sự tái hình dung cơ bản về KYC ở quy mô lớn.

---

## Kiến trúc giải pháp KYC gốc đám mây sử dụng AI tác nhân

![Kiến trúc tác nhân cấp cao cho KYC theo thời gian thực](/images/3-BlogsTraslated/kientruc.png)

---

## Các thành phần giải pháp

**Cơ sở hạ tầng giao tiếp hướng sự kiện với Amazon MSK**
- Amazon MSK đóng vai trò là xương sống giao tiếp, cho phép trao đổi tin nhắn không đồng bộ, theo thời gian thực giữa các thành phần AI tác nhân và các hệ thống doanh nghiệp. Cơ sở hạ tầng streaming được tổ chức thành các danh mục topic riêng biệt hỗ trợ luồng hai chiều.
- Các topic đầu vào (Inbound topics) thu thập tương tác của khách hàng thông qua các yêu cầu KYC (đơn đăng ký mới), tải lên tài liệu (giấy tờ tùy thân), kết quả xác minh ID (phản hồi từ nhà cung cấp bên thứ ba) và các sự kiện giao dịch (tín hiệu gian lận/rủi ro). 
- Các topic đầu ra (Outbound topics) xuất bản các quyết định KYC với điểm tin cậy và nhật ký kiểm toán đến các hệ thống ngân hàng lõi, định tuyến các trường hợp phức tạp đến người đánh giá thông qua các sự kiện quản lý trường hợp và kích hoạt cảnh báo gian lận cho các nhóm bảo mật. 
**Lớp điều phối AI tác nhân**
Tác nhân Giám sát Điều phối KYC

Tác nhân Giám sát triển khai logic định tuyến thông minh bằng cách sử dụng Amazon Bedrock AgentCore để xác định động các mẫu cộng tác tác nhân phụ tối ưu. 

**Kiến trúc quản lý tri thức thông minh**

- Cơ sở tri thức KYC triển khai một mẫu tạo sinh tăng cường truy xuất (RAG) để đưa ra các quyết định của tác nhân dựa trên thông tin thực tế, hiện tại thay vì chỉ dựa vào việc đào tạo foundation model. Amazon S3 lưu trữ các tài liệu nguồn, bao gồm các quy định từ các cơ quan tài chính, các quy tắc tuân thủ cụ thể của tổ chức, các chính sách nội bộ và tài liệu nhà cung cấp, được kích hoạt để theo dõi các thay đổi theo thời gian. 
- Truy xuất nhận biết ngữ cảnh làm phong phú các truy vấn bằng thông tin cụ thể của trường hợp, bao gồm khu vực pháp lý của khách hàng, loại tài liệu và mức độ rủi ro – tạo điều kiện thuận lợi cho hướng dẫn quy định có liên quan cao.
- Kho quyết định thời gian thực (Real-Time Decision Store) (Amazon DynamoDB) bổ sung cho Cơ sở tri thức với khả năng truy cập dưới mili giây vào dữ liệu có cấu trúc được truy cập thường xuyên, bao gồm trạng thái quyết định KYC hiện tại, điểm rủi ro, lịch sử tương tác của khách hàng và các tham số cấu hình động kiểm soát hành vi của tác nhân.

---

## Tích hợp an toàn với các hệ thống tài chính tại chỗ

Kiến trúc tích hợp với các hệ thống tài chính tại chỗ thông qua Action Groups, kết nối lớp tác nhân gốc đám mây và cơ sở hạ tầng doanh nghiệp hiện có.

Hệ thống quản lý khách hàng nhận các quyết định KYC theo thời gian thực, cập nhật trạng thái xác minh và cờ kích hoạt tài khoản. Hệ thống giám sát giao dịch tiêu thụ cảnh báo gian lận và điểm rủi ro, cho phép hành động ngay lập tức đối với các mẫu đáng ngờ. Hệ thống quản lý trường hợp nhận các trường hợp được leo thang với ngữ cảnh phân tích tác nhân toàn diện, đẩy nhanh quá trình xem xét của con người. Hệ thống rủi ro và AML tích hợp hai chiều để duy trì đánh giá rủi ro nhất quán. Hệ thống ngân hàng lõi nhận các xác thực đã được phê duyệt, kích hoạt kích hoạt tài khoản.

Kết nối an toàn thông qua AWS Direct Connect hoặc AWS Site-to-Site VPN cung cấp truyền dữ liệu được mã hóa qua các đường dẫn mạng chuyên dụng. Các lệnh gọi API bao gồm ghi nhật ký kiểm toán toàn diện thông qua AWS CloudTrail và Amazon CloudWatch, đáp ứng các yêu cầu quy định.


---

## Các cân nhắc về bảo mật

Giải pháp nên tích hợp các kiểm soát bảo mật đa lớp, giám sát liên tục và kiểm toán tuân thủ tự động để đáp ứng các kỳ vọng nghiêm ngặt của các cơ quan quản lý tài chính và các nhóm rủi ro nội bộ. Các tổ chức tài chính nên thực hiện mô hình hóa mối đe dọa toàn diện để xác định các rủi ro bao gồm cả những rủi ro do hệ thống AI tác nhân gây ra. Để biết thêm thông tin, vui lòng tham khảo Hướng dẫn bảo mật.

---

## Kết luận
Kiến trúc KYC này sử dụng các dịch vụ serverless của AWS và Amazon Bedrock để xử lý các xác thực nhanh hơn và ở quy mô lớn. Mô hình thực thi tác nhân song song được thiết kế để giảm thời gian xác thực KYC từ 3-5 ngày thông thường xuống gần thời gian thực cho các trường hợp tiêu chuẩn. Cách tiếp cận này cho phép xử lý nhanh hơn theo cấp số nhân thông qua hoạt động đồng thời của các tác nhân Phân tích tài liệu, Xác minh danh tính và Phát hiện gian lận thay vì các quy trình làm việc tuần tự.

Với kiến trúc này, các tổ chức tài chính có thể xử lý các xác thực khối lượng lớn thông qua khả năng mở rộng linh hoạt, tối ưu hóa chi phí thông qua mô hình định giá trả theo mức sử dụng serverless và cải thiện độ chính xác thông qua sự cộng tác của nhiều tác nhân

**Nguồn tham khảo:** <https://awsstudygroup.com/2026/05/26/hien-dai-hoa-kyc-voi-cac-giai-phap-serverless-cua-aws-va-ai-agent-cho-dich-vu-tai-chinh/>

---
