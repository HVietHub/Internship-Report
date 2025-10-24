---
title: "Blog 2"
date: 2025-09-09
weight: 1
chapter: false
pre: " <b> 3.2. </b> "
---


# 6 bài học mà các nhà phát triển game có thể học từ chiến lược quản trị đám mây của Epic Games

Quản trị đám mây (**cloud governance**) là điều cần thiết cho bất kỳ studio game nào muốn mở rộng quy mô trên đám mây.  
Chiến lược đúng đắn giúp đảm bảo **an ninh**, **tối ưu chi phí**, và mang lại **khả năng kiểm soát**.  

**Epic Games** – nhà phát triển *Fortnite* và **Unreal Engine** – đã xây dựng một chiến lược quản trị đám mây chặt chẽ với **AWS**.  
Bài viết này chia sẻ **sáu bài học** từ kinh nghiệm đó.

---

## 1. Thiết lập "lan can" (Guardrails)

- Xây dựng **chính sách & cấu trúc** ngay từ đầu.  
- Đảm bảo tuân thủ về bảo mật, gắn thẻ tài nguyên, và quyền truy cập.  
- Guardrails giúp duy trì kiểm soát mà không làm chậm sáng tạo.  

---

## 2. Tận dụng chuyên môn AWS

- Epic thành lập **hội đồng quản trị đám mây** (Cloud Governance Board).  
- Làm việc với **AWS Professional Services** và **Customer Solutions Managers**.  
- Thiết kế các tiêu chuẩn quản trị và cải tiến liên tục.  

---

## 3. Hiểu rõ quyền sở hữu

- Mỗi **tài khoản** hoặc **tài nguyên** có chủ sở hữu rõ ràng.  
- Quyền sở hữu gắn liền với kênh liên lạc để dễ xử lý sự cố.  
- Đảm bảo trách nhiệm và tối ưu hóa hiệu quả vận hành.  

---

## 4. Cấp quyền truy cập theo cơ chế tự phục vụ

- Kỹ sư có thể **tự yêu cầu quyền** và được phê duyệt nhanh chóng.  
- Tích hợp công cụ: **GitHub (IaC)**, **Okta**, **AWS Identity Center**, **SailPoint**.  
- Vừa tăng tốc độ, vừa duy trì kiểm soát.  

---

## 5. Tự động hóa cấu hình

- Sử dụng **AWS Step Functions** và **AWS Lambda** để tự động áp dụng:  
  - Mã hóa dữ liệu mặc định  
  - Bật logging  
  - Kiểm soát chi phí  
  - Tiêu chuẩn bảo mật trên toàn bộ tài khoản  

---

## 6. Thực thi tiêu chuẩn theo thời gian thực

- Sử dụng **Amazon EventBridge** để giám sát liên tục.  
- Tự động loại bỏ hoặc gắn cờ tài nguyên **không tuân thủ** (ví dụ: thiếu tag).  
- Tích hợp kiểm tra governance vào **GitHub Pull Request** để ngăn lỗi trước khi triển khai.  

---

## Kết quả đạt được

Nhờ chiến lược này, **Epic Games** đã:

- Giảm chi phí đám mây **~20%**  
- Đạt tỷ lệ gắn thẻ tài nguyên gần như **100%**  
- Tăng cường bảo mật và khả năng kiểm soát trên toàn hệ thống đám mây  

---

## Bài học cho studio game

Dù là **studio lớn hay nhỏ**, việc thiết lập chiến lược quản trị đám mây ngay từ đầu sẽ giúp:

- Giảm rủi ro  
- Tối ưu chi phí  
- Hỗ trợ sáng tạo và mở rộng quy mô  

---