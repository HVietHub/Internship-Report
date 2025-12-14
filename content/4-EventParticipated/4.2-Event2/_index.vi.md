---
title: "Sự kiện 2"
date: 2025-09-09
weight: 1
chapter: false
pre: " <b> 4.2. </b> "
---


# DevOps on AWS

### Thông Tin Sự Kiện

**Ngày:** Thứ Hai, 17 tháng 11, 2025  
**Thời gian:** 8:30 AM – 12:00 PM (Buổi sáng)  
**Địa điểm:** Tòa nhà Bitexco Financial Tower, TP.HCM  
**Diễn giả:** Trương Quan Tỉnh, Khả Vân, Bảo, Thịnh  
**Vai trò:** Người tham dự

---

## Tổng Quan Sự Kiện

Workshop cả ngày về văn hóa DevOps, dịch vụ AWS CI/CD và thực hành Infrastructure as Code.

### Chủ Đề Chính

**Tư Duy DevOps (8:30 – 9:00 AM)**
- Văn hóa và nguyên tắc DevOps
- DORA metrics: Deployment Frequency, Lead Time, MTTR, Change Failure Rate
- Lợi ích: Phân phối nhanh hơn, tỷ lệ lỗi thấp hơn, khôi phục nhanh hơn

**AWS CI/CD Pipeline (9:00 – 10:30 AM)**
- **CodeCommit**: Source control dựa trên Git
- **CodeBuild**: Build và test tự động
- **CodeDeploy**: Chiến lược triển khai
  - Blue/Green: Không downtime, rollback tức thì
  - Canary: Rollout dần dần với giám sát
  - Rolling: Cập nhật theo batch tiết kiệm chi phí
- **CodePipeline**: Điều phối từ đầu đến cuối
- Demo trực tiếp: CI/CD pipeline hoàn chỉnh

**Infrastructure as Code (10:45 AM – 12:00 PM)**
- **CloudFormation**: IaC gốc AWS với template
  - YAML/JSON khai báo
  - Quản lý stack và phát hiện drift
- **AWS CDK**: Cách tiếp cận ngôn ngữ lập trình
  - TypeScript, Python, Java, C#, Go
  - Construct cấp cao và pattern tái sử dụng
- Demo trực tiếp: Triển khai với cả hai công cụ

---

## Bài Học Rút Ra

**Văn Hóa DevOps:**
- Chuyển đổi văn hóa, không chỉ công cụ
- DORA metrics cung cấp đo lường khách quan
- Elite performer triển khai nhanh hơn 208 lần

**Phương Pháp Hay Nhất CI/CD:**
- Tự động hóa các giai đoạn source, build, test, deploy
- Chọn chiến lược triển khai dựa trên use case
- Triển khai test đúng cách ở mỗi giai đoạn
- Sử dụng phê duyệt thủ công cho production

**Triển Khai IaC:**
- CloudFormation cho IaC khai báo gốc AWS
- CDK cho cách tiếp cận ngôn ngữ lập trình với type safety
- Version control tất cả infrastructure code
- Triển khai drift detection

**Các Bước Tiếp Theo:**
- Đo lường DORA metrics hiện tại
- Triển khai CI/CD pipeline cho ứng dụng chính
- Chuyển đổi hạ tầng sang IaC (CloudFormation hoặc CDK)
- Theo đuổi chứng chỉ AWS DevOps

---

## Hình Ảnh Sự Kiện

![Workshop DevOps on AWS](/images/4-EvenPartcipated/eventmystery-1.jpg)

---

**Đánh giá:** ⭐⭐⭐⭐⭐ (5/5)

> Workshop DevOps toàn diện với demo CI/CD thực tế và chiến lược triển khai IaC rõ ràng.
