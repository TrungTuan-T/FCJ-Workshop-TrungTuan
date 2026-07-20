---
title: "Bản đề xuất"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# AWS Student Management Portal

## Cổng thông tin quản lý sinh viên chạy trên hạ tầng AWS

### 1. Tóm tắt điều hành

Hệ thống được thiết kế theo mô hình đám mây hiện đại, sử dụng kiến trúc không máy chủ (Serverless), tích hợp quy trình tự động hóa và đảm bảo an toàn thông tin đa lớp.

### 2. Tuyên bố vấn đề

 _Vấn đề hiện tại_

  * Các kiến trúc ứng dụng cũ, truyền thống thường gặp khó khăn trong việc phát hành sản phẩm, khiến thời gian release bị kéo dài, dẫn đến bỏ lỡ cơ hội kinh doanh và giảm doanh thu.
  * Hệ thống hoạt động kém hiệu quả khi lượng truy cập đột biến (ví dụ: mùa đăng ký tín chỉ, mùa nộp hồ sơ sinh viên), gây tốn kém chi phí duy trì server 24/7 nhưng hiệu suất vẫn không tối ưu.
  * Quy trình vận hành, deploy mã nguồn thủ công dễ xảy ra sai sót của con người (human error). Đồng thời, việc không tuân thủ nghiêm ngặt các quy định bảo mật hoặc quản lý quyền truy cập lỏng lẻo dễ làm mất an ninh thông tin và suy giảm uy tín hệ thống.



_Giải pháp_

  * Xây dựng hệ thống AWS Student Management Portal chuyển đổi hoàn toàn sang kiến trúc hiện đại: Tách biệt hệ thống thành cấu trúc Modular với các dịch vụ Microservices độc lập kết hợp mô hình không máy chủ Serverless (AWS Lambda, DynamoDB, API Gateway).
  * Áp dụng kiến trúc hướng sự kiện (Event-Driven Architecture) sử dụng Amazon SQS để xử lý các tác vụ bất đồng bộ và giao tiếp linh hoạt giữa các dịch vụ.
  * Bảo mật đa lớp từ tầng mạng (WAF, CloudFront), tầng định danh (Cognito) cho đến tầng dữ liệu (AWS KMS, IAM Role).
  * Tự động hóa hoàn toàn chu kỳ phát triển phần mềm (SDLC) bằng đường ống CI/CD tự động (CodeCommit → CodePipeline → CodeBuild).



_Lợi ích và hoàn vốn đầu tư (ROI)_

  * Tối ưu hiệu năng và chi phí (Cost & Performance Efficiency): Cơ chế Serverless loại bỏ hoàn toàn việc quản lý hạ tầng phần cứng. Hệ thống tự động mở rộng (Auto-scaling) ngay lập tức khi lượng truy cập tăng và áp dụng mô hình thanh toán theo giá trị thực tế (Pay-for-value) – chỉ tính tiền khi mã nguồn chạy.

  * Tính linh hoạt và khả năng chống chịu lỗi (Resilience & Agility): Nhờ cơ chế giao tiếp bất đồng bộ qua Queue, hệ thống có tính Loose-coupling cao, giúp giảm tải cho backend và đảm bảo hệ thống không bị sập khi quá tải cục bộ.

  * An toàn thông tin tuyệt đối: Giảm thiểu rủi ro rò rỉ dữ liệu nhờ cơ chế mã hóa lưu trữ tự động, phân quyền tối thiểu (IAM Role) và thiết lập chính sách sao lưu/khôi phục định kỳ (AWS Backup).

  * Tăng tốc độ phát triển sản phẩm: Quy trình CI/CD tự động hóa việc build và deploy giúp giảm thiểu sai sót thủ công, rút ngắn thời gian phát hành phiên bản mới.




### 3. Kiến trúc giải pháp

  * Hệ thống AWS Student Management Portal được xây dựng dựa trên kiến trúc Điện toán đám mây hiện đại (Cloud-Native Architecture), kết hợp chặt chẽ giữa mô hình Không máy chủ (Serverless) và cơ chế Hướng sự kiện (Event-Driven). Toàn bộ kiến trúc được phân tách thành các phân lớp chức năng độc lập, đảm bảo khả năng tự động mở rộng, tối ưu chi phí và bảo mật đa tầng.



![AWS Student Management Portal](/images/2-Proposal/aws.jpg)

_Dịch vụ AWS sử dụng_

  *  _Amazon Route 53:́́́́_ Dịch vụ quản lý và phân giải tên miền (DNS).
  * _Amazon CloudFront:_ Mạng lưới phân phối nội dung toàn cầu (CDN) giúp tăng tốc độ tải trang giao diện và lưu bộ nhớ đệm .
  * _AWS WAF:_ Tường lửa bảo vệ ứng dụng web trước các cuộc tấn công mạng độc hại.
  * _Amazon S3:_ Lưu trữ mã nguồn tĩnh frontend (Website hosting) và lưu trữ dữ liệu hồ sơ tải lên của sinh viên.
  * _Amazon Cognito:_ Dịch vụ quản lý định danh, xử lý xác thực đăng ký/đăng nhập và cấp mã bảo mật JWT Token cho người dùng.
  * _Amazon API Gateway:_ Cửa ngõ tiếp nhận, điều phối, định tuyến các yêu cầu API và thực hiện rate limiting.
  * _AWS Lambda:_ Dịch vụ tính toán Serverless (FaaS), trực tiếp thực thi mã nguồn xử lý logic backend một cách tự động theo sự kiện.
  * _Amazon DynamoDB:_ Cơ sở dữ liệu NoSQL tốc độ cao, độ trễ mili-giây, lưu trữ thông tin chi tiết của sinh viên.
  * _Amazon SQS:_ Hàng đợi thông điệp, hỗ trợ tiếp nhận và xử lý các tác vụ bất đồng bộ.
  * _Amazon SES:_ Dịch vụ gửi email tự động (gửi thông báo đến sinh viên/quản trị viên).
  * _Amazon CloudWatch:_ Công cụ giám sát hiệu năng hệ thống, thu thập log, metrics và thiết lập cảnh báo tự động (Alarm).
  * _Amazon SNS:_ Dịch vụ thông báo dạng Publish/Subscribe, dùng để gửi tin nhắn cảnh báo hệ thống đến đội ngũ quản trị.
  * _AWS KMS:_ Quản lý và khởi tạo khóa mã hóa dữ liệu lưu trữ tại chỗ.
  * _AWS Backup:_ Dịch vụ tập trung quản lý tự động việc sao lưu và khôi phục dữ liệu định kỳ theo chính sách.
  * _AWS CodeCommit:_ Kho lưu trữ mã nguồn bảo mật dựa trên Git.
  * _AWS CodeBuild:_ Dịch vụ biên dịch mã nguồn, chạy kiểm thử và đóng gói sản phẩm tự động.
  * _AWS CodePipeline:_ Dịch vụ điều phối chuỗi quy trình triển khai tự động liên tục (CI/CD).



_Thiết kế thành phần_

  *  _Amazon Route 53:_ Cấu hình A Record (Alias) trỏ tên miền về CloudFront.

  * _Amazon CloudFront:_ Tạo Distribution, cấu hình HTTPS và chính sách Cache tối ưu cho file tĩnh.

  * _AWS WAF:_ Gắn Web ACL vào CloudFront, bật các bộ quy tắc chống SQL Injection, XSS và giới hạn tần suất gọi API (Rate-limiting).

  * _Amazon S3 (Frontend):_ Bật Static Website Hosting, cấu hình OAC (Origin Access Control) chỉ cho phép CloudFront truy cập, chặn hoàn toàn truy cập công khai.

  * _Amazon S3 (Data Storage):_ Bật Versioning quản lý phiên bản, cấu hình CORS để tải tệp lên an toàn bằng Presigned URL.

  * _Amazon Cognito:_ Khởi tạo User Pool quản lý tài khoản sinh viên và cấp mã bảo mật JWT Token.

  * _Amazon API Gateway:_ Thiết lập các Endpoint dạng REST API, tích hợp Cognito Authorizer để chặn/cho phép request.

  * _AWS Lambda:_ Chạy code backend (Node.js/Python), tự động co giãn hiệu năng theo lượng event kích hoạt.

  * _Amazon DynamoDB:_ Cơ sở dữ liệu cấu hình chế độ On-Demand (tự động mở rộng sức chứa), đặt Khóa chính là StudentID.

  * _Amazon SQS & SES:_ Cấu hình hàng đợi Standard/FIFO xử lý bất đồng bộ và gọi API SES gửi email tự động đã xác thực cấu hình DKIM/SPF.

  * _CloudWatch & SNS:_ Tạo các Metric Filters lọc lỗi hệ thống, cấu hình Alarm gửi tin nhắn cảnh báo tức thời qua SNS Topic.

  * _AWS KMS & Backup:_ Mã hóa dữ liệu lưu trữ tự động (Data-at-rest) và đặt chính sách sao lưu định kỳ, sẵn sàng khôi phục thảm họa.

  * _CI/CD (Code Suite):_ Tự động hóa quy trình build và deploy liên tục .




### 4. Triển khai kỹ thuật

 _Yêu cầu kỹ thuật_

  *  _Compute & Logic Layer:_ AWS Lambda (chạy backend xử lý bất đồng bộ và logic quản lý), Amazon API Gateway.

  * _Storage & Database:_ Amazon DynamoDB (Cơ sở dữ liệu lưu trữ thông tin sinh viên), Amazon S3 (Lưu trữ frontend tĩnh và tệp tài liệu sinh viên).

  * _Security & Identity:_ Amazon Cognito, IAM Roles, AWS KMS (Mã hóa khóa bảo mật).

  * _Observability:_ Amazon CloudWatch (Log & Metrics), CloudWatch Alarms, Amazon SNS (Hệ thống điều phối cảnh báo).

  * _Data Protection & Resiliency:_ AWS Backup (Chính sách sao lưu và khôi phục thảm họa định kỳ).

  * _CI/CD Automation:_ AWS CodeCommit (Quản lý mã nguồn), AWS CodeBuild (Đóng gói/Kiểm thử), AWS CodePipeline (Tự động hóa deploy liên tục).




### 5. Lộ trình & Mốc triển khai

  *  _Giai đoạn 1:_ Thiết lập hạ tầng và Cấu hình bảo mật dữ liệu



Triển khai các dịch vụ cốt lõi và thiết lập cơ chế an toàn thông tin đa lớp.

Sử dụng AWS KMS để thực hiện mã hóa toàn bộ dữ liệu tại chỗ (Data-at-rest) lưu trữ trên Amazon DynamoDB và Amazon S3.

Thiết lập hệ thống IAM Role nghiêm ngặt theo nguyên tắc đặc quyền tối thiểu (Least Privilege), đảm bảo mỗi dịch vụ chỉ có quyền truy cập vừa đủ vào tài nguyên cần thiết.

  * _Giai đoạn 2:_ Cấu hình hệ thống giám sát và Vận hành



Tích hợp Amazon CloudWatch để thu thập tập trung dữ liệu log, chỉ số hiệu năng (metrics) từ AWS Lambda và API Gateway.

Thiết lập các CloudWatch Alarms theo dõi ngưỡng lỗi (như lỗi Lambda crash hoặc API Gateway quá tải).

Kết nối hệ thống báo động với Amazon SNS để tự động gửi cảnh báo tức thời tới đội ngũ kỹ sư quản trị ngay khi phát hiện dấu hiệu bất thường.

  * _Giai đoạn 3:_ Tự động hóa đường ống triển khai (CI/CD Pipeline)



Thiết lập kho lưu trữ mã nguồn an toàn dựa trên Git thông qua AWS CodeCommit.

Sử dụng AWS CodeBuild để tự động hóa quy trình biên dịch, chạy các bài kiểm thử và đóng gói sản phẩm.

Cấu hình luồng điều phối liên tục AWS CodePipeline giúp tự động cập nhật Frontend lên Amazon S3 và triển khai Backend lên AWS Lambda mỗi khi có cập nhật mã nguồn mới.

  * _Giai đoạn 4:_ Thiết lập cơ chế dự phòng và Sao lưu định kỳ



Cấu hình dịch vụ AWS Backup tập trung để quản lý toàn diện các bản sao lưu dữ liệu cho DynamoDB và S3.

Cài đặt chính sách lưu trữ và khôi phục định kỳ, sẵn sàng ứng phó trong các tình huống khẩn cấp hoặc sự cố thảm họa dữ liệu.

### 6. Ước tính ngân sách

 _Chi phí hạ tầng_

Dịch vụ AWS| Mức chi phí ước tính/tháng  
---|---  
Amazon S3 (frontend + tài liệu)| $3–$8  
Amazon CloudFront| $5–$15  
Amazon API Gateway| $3–$8  
AWS Lambda| $2–$10  
Amazon DynamoDB| $10–$25  
Amazon Cognito| $0–$10  
Amazon SQS| $0–$2  
Amazon SES| $0–$10  
Amazon CloudWatch Logs| $2–$8  
  
**Tổng ước tính:** khoảng $25–$96/tháng, tùy mức truy cập, dung lượng lưu trữ và số lượng email gửi.

### 7. Đánh giá rủi ro

Dưới đây là các rủi ro chính khi triển khai và vận hành hệ thống AWS Student Management Portal:

Loại rủi ro| Mô tả| Mức độ| Biện pháp giảm thiểu  
---|---|---|---  
Bảo mật| Token Cognito, IAM role hoặc cấu hình S3 có thể bị khai thác nếu không kiểm soát đúng| Cao| Dùng Cognito Authorizer, giới hạn quyền IAM, khóa public access cho bucket, bật CloudFront và bảo mật biến môi trường  
Dữ liệu| Dữ liệu sinh viên và hồ sơ có thể bị mất hoặc không nhất quán nếu schema thay đổi hoặc lỗi xử lý| Trung bình| Backup DynamoDB, validate dữ liệu đầu vào, dùng logging và retry logic  
Triển khai| Cấu hình API Gateway, Lambda, Cognito và S3 có thể sai dẫn đến hệ thống không chạy được| Trung bình| Kiểm thử từng thành phần, dùng môi trường dev/test trước khi deploy production  
Chi phí vận hành| Số lượng request Lambda, DynamoDB read/write và lưu trữ file có thể làm chi phí tăng nhanh| Trung bình| Tối ưu Lambda, dùng DynamoDB on-demand, giới hạn log và file lưu trữ  
Vận hành| Lambda cold start, lỗi timeout, SQS/SES không hoạt động đúng có thể ảnh hưởng trải nghiệm người dùng| Trung bình| Cấu hình timeout hợp lý, theo dõi CloudWatch, thiết lập alert và retry  
Hạn chế email| SES ở chế độ sandbox có thể giới hạn người nhận và gây lỗi gửi email| Trung bình| Verify sender/recipient trước khi dùng, kiểm tra cấu hình SES và queue worker  
  
### 8. Kết quả kỳ vọng

Dự án này được kỳ vọng mang lại các giá trị chính sau:

  * Hiểu rõ cách xây dựng một ứng dụng web theo mô hình serverless trên AWS.
  * Thực hành kết nối các dịch vụ AWS như S3, CloudFront, Cognito, API Gateway, Lambda, DynamoDB, SQS, SES và CloudWatch.
  * Tạo được một nền tảng quản lý sinh viên có thể mở rộng cho các hệ thống thực tế về sau.
  * Hỗ trợ việc học tập, demo và báo cáo triển khai cloud một cách trực quan và có cấu trúc.
  * Tạo tiền đề cho các dự án nâng cao như phân quyền chi tiết, dashboard thống kê, CI/CD hoặc triển khai production.



Kỳ vọng thực tế của dự án là không chỉ dừng ở giao diện demo, mà còn thể hiện được cách thiết kế kiến trúc, xử lý dữ liệu, bảo mật và vận hành hệ thống trên nền tảng cloud.
