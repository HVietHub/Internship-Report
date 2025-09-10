---
title: "Blog 1"
date: 2025-09-09
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# Trao quyền cho khách hàng triển khai AI Agent sẵn sàng cho sản xuất ở quy mô lớn

*Bởi Swami Sivasubramanian, ngày 16/07/2025 – trong các dịch vụ Amazon Bedrock, Amazon Connect, Amazon Nova, Amazon Q, Amazon S3, AWS Inferentia, AWS Trainium, AWS Transform, Featured, Thought Leadership*

---

## Tầm nhìn về AI Agent

AI Agent sẽ thay đổi cách chúng ta làm việc và sống. CEO AWS, Matt Garman, đã chia sẻ rằng đây là một sự chuyển đổi công nghệ mang tính cách mạng, tương tự như sự ra đời của Internet.  

Các hệ thống **agent thông minh** đã bắt đầu giải quyết những vấn đề phức tạp, tự động hóa quy trình và mở ra tiềm năng mới trong nhiều ngành:  
- **AstraZeneca**: tăng tốc khám phá tri thức y tế  
- **Yahoo Finance**: cải thiện nghiên cứu tài chính cho hàng triệu nhà đầu tư  
- **Syngenta**: cách mạng hóa nông nghiệp với nông nghiệp chính xác dựa trên AI  

Để mở rộng từ những thành công ban đầu sang ứng dụng rộng rãi, các tổ chức cần một cách tiếp cận thực tiễn để xử lý **độ phức tạp vốn có** của agentic systems. AWS cam kết trở thành nơi tốt nhất để xây dựng các AI Agent hữu ích nhất thế giới, an toàn và có thể mở rộng.  

---

## Nguyên tắc định hướng (Guiding Principles)

Từ kinh nghiệm hỗ trợ hàng trăm nghìn khách hàng, AWS đã đúc kết 4 nguyên tắc cốt lõi cho **AI Agent**:

### **Nguyên tắc 1: Linh hoạt là lợi thế cạnh tranh**
- Tương lai khó dự đoán, thành công thuộc về tổ chức có khả năng **thích ứng nhanh**.  
- Hệ thống agent cần **kiến trúc mở, linh hoạt**, dễ tích hợp với dữ liệu riêng và công cụ sẵn có.  
- AWS giới thiệu **Amazon Bedrock AgentCore** – cung cấp môi trường serverless an toàn, cô lập phiên, có khả năng mở rộng, và tích hợp với framework mã nguồn mở (CrewAI, LangGraph, LlamaIndex…).  

**AgentCore giúp doanh nghiệp vượt qua giai đoạn thử nghiệm để tiến tới triển khai thực tế ở quy mô lớn.**

---

### **Nguyên tắc 2: Nâng cấp nền tảng cho kỷ nguyên agent**
Các yêu cầu cơ bản (bảo mật, tin cậy, quan sát…) vẫn còn, nhưng cách triển khai cần thay đổi:

- **Bảo mật & Tin cậy**: mỗi phiên chạy trong môi trường tính toán riêng, cô lập bộ nhớ để tránh rò rỉ dữ liệu.  
- **Độ tin cậy & khả năng mở rộng**: hỗ trợ checkpointing, tự động phục hồi, mở rộng từ 0 → hàng ngàn phiên.  
- **Danh tính & quyền truy cập**: hỗ trợ quyền tạm thời, chi tiết, tích hợp với Cognito, Okta, Microsoft Entra ID, GitHub, Google…  
- **Quan sát (Observability)**: giám sát theo thời gian thực, dashboard tích hợp OpenTelemetry.  
- **Dữ liệu**: AgentCore Gateway giúp kết nối dữ liệu riêng (Knowledge Base, API) thành công cụ cho agent.  
- **Tích hợp liền mạch**: biến API/dịch vụ thành công cụ agent-compatible chỉ với vài dòng code.  
- **Bộ công cụ & bộ nhớ**: AgentCore Memory hỗ trợ trí nhớ ngắn hạn và dài hạn, AgentCore Browser để duyệt web, AgentCore Code Interpreter để thực thi code an toàn.  

Đây là nền tảng để xây dựng **kiến trúc agent an toàn, tin cậy, và sẵn sàng cho sản xuất**.

---

### **Nguyên tắc 3: Kết hợp lựa chọn mô hình & dữ liệu riêng**
- Mỗi ứng dụng có yêu cầu khác nhau → không có mô hình nào tối ưu cho tất cả.  
- AWS (từ 2023) với **Amazon Bedrock** đã tiên phong trong việc **tự do chọn mô hình**.  
- Hôm nay, AWS mở rộng với **Amazon Nova** + **SageMaker AI** cho phép:  
  - Fine-tuning (PEFT, full fine-tuning, SFT, DPO, PPO, CPT, Knowledge Distillation).  
  - Tích hợp mô hình tùy biến trực tiếp với Bedrock.  
- **Nova Act**: mô hình chuyên cho tự động hóa trên trình duyệt, với SDK riêng.  
- **Amazon S3 Vectors**: kho lưu trữ vector tích hợp, giảm chi phí 90%, tốc độ truy vấn <1 giây.  

**Kết hợp mô hình + dữ liệu riêng → AI có tri thức chuyên sâu, hiệu quả và tiết kiệm.**

---

### **Nguyên tắc 4: Giải pháp biến đổi trải nghiệm**
- AI Agent không chỉ thay đổi hạ tầng, mà **tái cấu trúc toàn bộ quy trình làm việc**.  
- AWS cung cấp **giải pháp dựng sẵn** để tăng tốc:  
  - **AWS Marketplace**: mua agent & tool từ đối tác AWS.  
  - **Kiro**: IDE AI hỗ trợ từ ý tưởng → code, test, docs.  
  - **AWS Transform**: agent chuyên tự động hóa hiện đại hóa ứng dụng (refactor, mapping).  
  - **Amazon Connect**: mang AI vào mọi điểm chạm khách hàng.  

Giúp tổ chức đi từ **thử nghiệm** → **chiến lược AI toàn diện** nhanh hơn.  

---

## Con đường phía trước

- AWS đang tăng gấp đôi đầu tư, thêm **100 triệu USD cho Generative AI Innovation Center**.  
- Đã hỗ trợ NFL, Yahoo Finance, BMW, AstraZeneca… đạt **hàng triệu USD lợi ích năng suất**.  
- AWS tiếp tục giữ vững nguyên tắc: **bảo mật, độ tin cậy, quyền riêng tư dữ liệu**.  

**Thông điệp quan trọng nhất: hãy bắt đầu ngay.**  
Chọn một vấn đề cụ thể trong doanh nghiệp, xây dựng agent, học hỏi từ thực tế → đó là con đường thành công nhanh nhất.  

---

## Kết luận

Với 4 nguyên tắc:  
1. **Linh hoạt**,  
2. **Nâng cấp nền tảng**,  
3. **Kết hợp mô hình + dữ liệu riêng**,  
4. **Triển khai giải pháp biến đổi**,  

AWS mang lại nền tảng toàn diện để doanh nghiệp xây dựng **AI Agent sẵn sàng sản xuất ở quy mô lớn**.  

Tương lai AI agentic đã đến, và AWS muốn cùng bạn **tái định nghĩa điều gì là có thể cho doanh nghiệp của bạn**.  
