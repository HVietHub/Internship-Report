---
title: "Bản đề xuất"
date: 2025-09-09
weight: 2
chapter: false
pre: " <b> 2. </b> "
---


# Mini Food Social  
## Nền tảng mạng xã hội ẩm thực Serverless với tính năng gợi ý công thức bằng AI

### 1. Tóm tắt dự án
**Mini Food Social** là một nền tảng mạng xã hội ẩm thực được xây dựng hoàn toàn theo kiến trúc **serverless**, tích hợp AI để hỗ trợ sáng tạo công thức món ăn.  
Người dùng có thể đăng bài, chia sẻ hình ảnh món ăn, và nhận gợi ý công thức nấu ăn được sinh tự động bởi AI dựa trên nguyên liệu hoặc khẩu vị.  
Ứng dụng hỗ trợ tối đa **100 người dùng đăng ký**, với các tính năng: đăng nhập, đăng bài, bình luận, thích, và gợi ý món ăn thông minh.

Hệ thống sử dụng các dịch vụ được quản lý toàn phần của AWS, đảm bảo khả năng mở rộng, bảo mật và chi phí tối ưu:
- **AWS Amplify** (Lưu trữ & CI/CD frontend Next.js)  
- **Amazon API Gateway** + **AWS Lambda** (Xử lý backend)  
- **Amazon DynamoDB** (CSDL NoSQL)  
- **Amazon Bedrock** (AI sinh công thức)  
- **Amazon Cognito** (Xác thực người dùng)  
- **Amazon CloudFront + WAF + Route 53** (Phân phối & bảo mật nội dung)  

Giải pháp mang lại nền tảng ổn định, chi phí thấp và dễ mở rộng cho ứng dụng web hiện đại.

---

### 2. Vấn đề và giải pháp
#### Vấn đề hiện tại
Các nền tảng chia sẻ công thức hiện nay thường thiếu tính cá nhân hóa và tương tác.  
Người dùng mong muốn:
- Cộng đồng tương tác mạnh mẽ (đăng bài, bình luận, thích bài)  
- Gợi ý công thức thông minh bằng AI  
- Đăng nhập dễ dàng, bảo mật cao  

Tuy nhiên, việc vận hành hệ thống như vậy thường tốn kém và phức tạp khi mở rộng.

#### Giải pháp đề xuất
**Mini Food Social** áp dụng kiến trúc **Serverless** và **Event-driven** hoàn toàn trên AWS:
- **Amplify** triển khai frontend qua GitLab CI/CD.  
- **Cognito** quản lý đăng nhập, xác thực, và token JWT.  
- **API Gateway** định tuyến request đến **Lambda**.  
- **Lambda Router** xử lý logic (bài đăng, người dùng, AI).  
- **Bedrock** sinh công thức món ăn dựa trên prompt người dùng.  
- **CloudFront + WAF** phân phối nhanh và đảm bảo an toàn.  

---

### Lợi ích & Hiệu quả đầu tư
- **Chi phí thấp** — Trả tiền theo mức sử dụng, không cần quản lý máy chủ.  
- **Tự động mở rộng** — Hoạt động ổn định khi tăng lượng người dùng.  
- **Tích hợp AI** — Nâng cao trải nghiệm và giữ chân người dùng.  
- **Bảo mật cao** — Cognito xác thực, dữ liệu mã hóa trong S3 & DynamoDB.  

**Chi phí hàng tháng ước tính (không tính Free Tier): ≈ $15.40 USD**  
**Chi phí hàng năm: ≈ $184.80 USD**

---

### 3. Kiến trúc hệ thống
Hệ thống được chia thành **5 lớp Serverless**, giúp quản lý độc lập và dễ mở rộng:

- **Lớp CI/CD:** GitLab tự động triển khai — **Amplify** cho frontend, **CDK + CloudFormation** cho backend.  
- **Lớp Edge:** Bảo mật & phân phối lưu lượng với **Route 53** (DNS), **WAF** (Web Application Firewall), và **CloudFront** (CDN).  
  CloudFront truy cập **S3** qua **OAI (Origin Access Identity)** để tăng bảo mật.  
- **Lớp Ứng dụng:** **Amplify** lưu trữ frontend (Next.js); **API Gateway + Lambda** xử lý backend; **Cognito** xác thực & cấp token.  
- **Lớp Dữ liệu:** **DynamoDB** lưu dữ liệu bài đăng và người dùng; **S3** lưu hình ảnh; **EventBridge** xử lý sự kiện bất đồng bộ.  
- **Lớp Quan sát:** **CloudWatch** (giám sát), **X-Ray** (truy vết request), **SNS** (gửi thông báo chi phí và cảnh báo).

![Mini Food Social Architecture](/images/2-Proposal/FsocialArchitecture.png)

### Dịch vụ AWS được sử dụng
| Danh mục | Dịch vụ |
|-----------|----------|
| **Frontend & CI/CD** | AWS Amplify (Next.js), GitLab, AWS CDK, CloudFormation |
| **Edge & Bảo mật** | Route 53, CloudFront, WAF |
| **Ứng dụng & Xử lý** | API Gateway, AWS Lambda, Amazon Cognito |
| **Dữ liệu & AI** | DynamoDB (single-table), S3, Amazon Bedrock |
| **Quan sát & Sự kiện** | CloudWatch, X-Ray, SNS, EventBridge |

---

### Thiết kế thành phần
- **Truy cập người dùng:** User → CloudFront → Amplify (Frontend)  
- **Xác thực:** CloudFront → Cognito (Hosted UI redirect)  
- **API:** Amplify → Route 53 → CloudFront → WAF → API Gateway  
- **Ủy quyền:** API Gateway → Cognito Authorizer  
- **Xử lý backend:** API Router Lambda → DynamoDB / S3 / Bedrock  
- **Sinh công thức AI:** AI Handler Lambda → Bedrock (Claude model)  
- **Phân phối hình ảnh:** CloudFront → S3 (qua OAI)  
- **Giám sát & sự kiện:** EventBridge, CloudWatch, X-Ray, SNS  

---

### 4. Triển khai kỹ thuật
#### Các giai đoạn triển khai
| Giai đoạn | Thời gian | Mô tả |
|------------|------------|-------|
| **1. Thiết lập & CI/CD** | Tuần 1–3 | Cấu hình CDK, kết nối Amplify và GitLab |
| **2. Backend cốt lõi** | Tuần 4–7 | Cognito, API Gateway, Lambda Router |
| **3. Frontend** | Tuần 8–10 | Tích hợp Next.js UI và API |
| **4. AI & Giám sát** | Tuần 11–13 | Tích hợp Bedrock, CloudWatch, X-Ray |
| **5. Kiểm thử cuối** | Tuần 14 | Load test & triển khai Production |

#### Yêu cầu kỹ thuật
- **Frontend:** Next.js (Amplify Hosting)  
- **Backend:** Node.js (Lambda), API Gateway, Cognito  
- **AI:** Amazon Bedrock (Claude)  
- **CSDL:** DynamoDB (single-table, GSI)  
- **DevOps:** GitLab CI/CD cho Amplify & CDK  

---

### 5. Lộ trình & Mốc hoàn thành
| Tháng | Kết quả |
|--------|----------|
| **Tháng 1** | Hoàn thiện thiết kế kiến trúc & CI/CD |
| **Tháng 2** | Hoàn thiện xác thực & backend |
| **Tháng 3** | Tích hợp frontend, AI & quan sát hệ thống |
| **Cuối tháng 3** | ✅ Triển khai bản Production |

---

### 6. Ước tính chi phí
#### Chi phí dịch vụ AWS (100 người dùng hoạt động, không tính Free Tier)
| Dịch vụ | Mô tả | Chi phí tháng (USD) |
|----------|--------|---------------------|
| **AWS Amplify** | Lưu trữ & CI/CD frontend | **$2.00** |
| **Amazon CloudFront** | CDN phân phối toàn cầu (100GB) | **$5.00** |
| **AWS WAF** | 1 Web ACL + 1 rule cơ bản | **$5.00** |
| **Amazon API Gateway** | ~25,000 request/tháng | **$0.40** |
| **AWS Lambda** | 4 hàm, ~1M request/tháng | **$0.80** |
| **Amazon DynamoDB** | On-demand mode | **$0.60** |
| **Amazon S3** | 5GB file + 20GB hình ảnh | **$0.50** |
| **Amazon Cognito** | 100 người dùng/tháng | **$0.50** |
| **Amazon Bedrock** | Sinh văn bản (~50K tokens) | **$2.50** |
| **Amazon Route 53** | Tên miền + DNS | **$1.30** |
| **CloudFormation / SNS / X-Ray** | Hạ tầng + giám sát | **$0.00** |

**➡ Tổng chi phí hàng tháng:** ≈ **$15.40 USD**  
**➡ Chi phí hàng năm:** ≈ **$184.80 USD**

---

### 7. Đánh giá rủi ro
| Rủi ro | Mức độ ảnh hưởng | Xác suất |
|--------|------------------|----------|
| Sử dụng Bedrock vượt dự kiến | Trung bình | Trung bình |
| Cấu hình sai Cognito | Cao | Trung bình |
| Rule WAF chặn nhầm traffic | Trung bình | Thấp |

**Giải pháp giảm thiểu:**  
- Thiết lập cảnh báo chi phí CloudWatch cho Bedrock.  
- Áp dụng nguyên tắc **IAM least privilege** cho Lambda.  
- Kiểm thử rule WAF trong môi trường staging trước production.

---

### 8. Kết quả kỳ vọng
#### Kết quả kỹ thuật
- Hoàn thiện nền tảng **mạng xã hội ẩm thực serverless** có tích hợp AI.  
- Hệ thống bảo mật và mở rộng tốt với **Cognito**, **WAF**, **CloudFront**.  
- Giám sát và cảnh báo tự động bằng **CloudWatch** và **X-Ray**.

#### Giá trị lâu dài
- Thể hiện năng lực thực hành **Serverless, DevOps, AI Integration**.  
- Có thể mở rộng lên **500+ người dùng** mà không cần thay đổi kiến trúc.  
- Là **mẫu kiến trúc chuẩn AWS** có thể tái sử dụng cho các dự án sau.