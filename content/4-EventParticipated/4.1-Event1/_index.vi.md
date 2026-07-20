---
title: "Event 1"
date: 2026
weight: 1
chapter: false
pre: " <b> 4.1. </b> "
---


# Bài thu hoạch “Automated Prompt Engineering Echancing LLM Output Quality”

### Mục Đích Của Sự Kiện

- Giới thiệu về một tiện ích mở rộng trên trình duyệt
- Cho phép người dùng trò chuyện với AI ở bất kỳ đâu trên web và tự động tối ưu hóa các câu lệnh
### Danh Sách Diễn Giả

- **Nguyen Tuan Thinh** - DevOps/Cloud Engineer 
- **...**
### Nội Dung Nổi Bật

#### Prompt Engineering 

- Người dùng phổ thông thường viết prompt rất ngắn và chung chung -> việc AI phản hồi sai lệch -> tốn chi phí token và làm giảm năng suất.

Giải pháp cấu trúc hóa: Bài viết đưa ra công thức chuẩn để tối ưu hóa một câu lệnh gồm đầy đủ 7 thành phần cấu thành . Điều này giúp thay đổi tư duy dùng AI từ việc đưa ra thông tin không đúng thiếu chính xác sang làm việc có cấu trúc.
#### Kỹ thuật tư duy nâng cao cho AI - Advanced Techniques

Điểm nhấn học thuật trong bài chính là các phương pháp giúp kích thích khả năng lập luận của các mô hình ngôn ngữ lớn (LLM):

- **Chain-of-Thought (CoT):** Ép AI phải suy nghĩ và giải thích từng bước trước khi đưa ra đáp án, giúp tăng độ chính xác.
- **Tree-of-Thoughts (ToT):** Cho phép AI tự khám phá và thử nghiệm nhiều hướng giải quyết khác nhau để tự tìm ra phương án tối ưu nhất.
- **Token Economics:** hấn mạnh bài toán chi phí trong doanh nghiệp – hiểu rõ cách tính Token giữa các ngôn ngữ và sự chênh lệch giá giữa dữ liệu đầu vào (Input) và đầu ra (Output) để thiết kế prompt tiết kiệm nhất.

#### Kiến trúc hệ thống Serverless linh hoạt - AWS Solution Architecture

- **Trải nghiệm người dùng liền mạch:** Kết hợp giữa Chrome Extension và Amazon Cognito giúp tự động đồng bộ hóa trạng thái đăng nhập và mã token bảo mật của người dùng.
- **Tối ưu hóa hiệu năng & Chi phí:** Toàn bộ hệ thống chạy trên AWS Lambda và API Gateway. Hệ thống chỉ hoạt động và tính phí khi có yêu cầu từ người dùng, có khả năng tự động mở rộng (Scale) vô hạn khi lượng truy cập tăng cao mà không cần quản trị server.
- **Trực tiếp kết nối AI qua Amazon Bedrock:** Thay vì phải tốn chi phí và thời gian tự huấn luyện mô hình, dự án sử dụng Amazon Bedrock để gọi trực tiếp các mô hình AI hàng đầu thế giới (như Claude, GPT), giúp bộ máy tối ưu hóa câu lệnh vận hành mượt mà và linh hoạt.

### Những Gì Học Được

#### Tư Duy Thiết Kế

- **Hiểu sâu sắc về bản chất của Prompt:** Học được rằng Prompt không đơn thuần là một câu hỏi câu lệnh bản năng, mà là một cấu trúc có kỹ nghệ bao gồm nhiều thành phần (Role, Context, Instruction, Constraints, Few-shot) để định hình tư duy cho AI.
- **Tư duy tối ưu chi phí (Token Economics)**: Ý thức được rằng mỗi ký tự gửi đi và nhận về đều tiêu tốn chi phí (Token). Từ đó học được cách viết ngắn gọn, súc tích, sử dụng ký tự phân tách để vừa tăng hiệu quả vừa tiết kiệm tài nguyên hệ thống.

#### Kiến Trúc Đám Mây 

- **Sức mạnh của kiến trúc Serverless:** Hiểu cách thiết kế một hệ thống hiện đại, tự động mở rộng (Auto-scaling) hoàn toàn bằng các dịch vụ Serverless của AWS như AWS Lambda và API Gateway. Không còn tư duy quản lý server vật lý hay server ảo truyền thống, giúp tối ưu chi phí vận hành (chỉ tính tiền khi có request).
- **Tích hợp Generative AI vào ứng dụng thực tế:** Biết cách tận dụng Amazon Bedrock để kết nối trực tiếp với các mô hình ngôn ngữ lớn (LLM) hàng đầu hiện nay một cách nhanh chóng, an toàn bảo mật dữ liệu mà không cần phải tự đầu tư hạ tầng phần cứng để huấn luyện lại mô hình từ đầu.

### Trải nghiệm trong event
- **Môi trường học tập chuyên nghiệp:** Sự kiện mang lại một không gian đậm chất công nghệ, kết nối giữa lý thuyết học thuật và ứng dụng thực tế trong doanh nghiệp.

- **Được biết thêm về cách làm dự án:** Được lắng nghe chia sẻ từ anh Nguyễn Tuấn Thịnh (DevOps/Cloud Engineer), giúp thu hẹp khoảng cách giữa kiến thức nhà trường và yêu cầu thực tế của thị trường lao động. Không khí cởi mở khuyến khích việc đặt câu hỏi và phản biện.


#### Một số hình ảnh khi tham gia sự kiện
* Em không có lưu về máy ạ
> Tổng thể, sự kiện không chỉ cung cấp kiến thức kỹ thuật mà còn giúp em thay đổi cách tư duy về thiết kế ứng dụng, hiện đại hóa hệ thống và phối hợp hiệu quả hơn giữa các team.

