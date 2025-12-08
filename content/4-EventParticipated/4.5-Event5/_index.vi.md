---
title: "Sự kiện 5"
date: 2025-11-29
weight: 5
chapter: false
pre: " <b> 4.5. </b> "
---

# Workshop AWS Well-Architected Security Pillar

### Thông Tin Sự Kiện

**Ngày:** Thứ Sáu, 29 tháng 11, 2025  
**Thời gian:** 8:30 AM – 12:00 PM (Chỉ buổi sáng)  
**Địa điểm:** Văn phòng AWS Vietnam, Tòa nhà Bitexco Financial Tower, 2 Đường Hải Triều, Quận 1, TP.HCM  
**Diễn giả:** Trần Toàn Công Lý, Hoàng Anh, Trần Đức Anh, Nguyễn Tuấn Thịnh, Nguyễn Đỗ Thanh Đạt, Hoàng Kha, Thịnh Lâm, Việt Nguyễn, Mende Grabski (Long), Tĩnh Trường  
**Vai trò:** Người tham dự

---

## Tổng Quan Sự Kiện

Workshop toàn diện về AWS Well-Architected Security Pillar, bao gồm năm trụ cột cơ bản của bảo mật cloud với các demo thực tế và tình huống thực tế từ doanh nghiệp Việt Nam.

### Lịch Trình Buổi Học

**8:30 – 8:50 AM | Khai Mạc & Nền Tảng Bảo Mật**
*Diễn giả: Trần Toàn Công Lý*

- **Vai Trò Security Pillar trong Well-Architected Framework:**
  - Nguyên tắc cốt lõi: Least Privilege, Zero Trust, Defense in Depth
  - Mô hình Trách nhiệm Chia sẻ của AWS
  - Các mối đe dọa hàng đầu trong môi trường cloud tại Việt Nam

---

**⭐ Trụ Cột 1 — Identity & Access Management**

**8:50 – 9:30 AM | Kiến Trúc IAM Hiện Đại**
*Diễn giả: Hoàng Anh*

- **Kiến Thức Cơ Bản IAM:**
  - Users, Roles, Policies – tránh long-term credentials
  - IAM Identity Center: SSO và permission sets
  - Service Control Policies (SCP) & permission boundaries cho multi-account
  - MFA, credential rotation, Access Analyzer

- **Mini Demo:**
  - Validate IAM Policy
  - Mô phỏng các tình huống truy cập

---

**⭐ Trụ Cột 2 — Detection**

**9:30 – 9:55 AM | Detection & Continuous Monitoring**
*Diễn giả: Trần Đức Anh, Nguyễn Tuấn Thịnh, Nguyễn Đỗ Thanh Đạt*

- **Dịch Vụ Giám Sát:**
  - CloudTrail (logging cấp tổ chức)
  - GuardDuty (phát hiện mối đe dọa)
  - Security Hub (tổng hợp bảo mật tập trung)

- **Logging Toàn Diện:**
  - VPC Flow Logs
  - ALB/S3 access logs
  - Cảnh báo & tự động hóa với EventBridge
  - Detection-as-Code (infrastructure + rules)

**9:55 – 10:10 AM | Giải Lao**

---

**⭐ Trụ Cột 3 — Infrastructure Protection**

**10:10 – 10:40 AM | Network & Workload Security**
*Diễn giả: Hoàng Kha*

- **Bảo Mật Mạng:**
  - Chiến lược phân đoạn VPC
  - Vị trí private vs public subnet
  - Security Groups vs NACLs: mô hình áp dụng

- **Dịch Vụ Bảo Vệ:**
  - AWS WAF (Web Application Firewall)
  - AWS Shield (bảo vệ DDoS)
  - AWS Network Firewall

- **Bảo Vệ Workload:**
  - Cơ bản bảo mật EC2
  - Bảo mật container ECS/EKS

---

**⭐ Trụ Cột 4 — Data Protection**

**10:40 – 11:10 AM | Encryption, Keys & Secrets**
*Diễn giả: Thịnh Lâm, Việt Nguyễn*

- **Quản Lý Khóa:**
  - AWS KMS: key policies, grants, rotation
  - Mã hóa at-rest: S3, EBS, RDS, DynamoDB
  - Mã hóa in-transit: TLS/SSL

- **Quản Lý Secrets:**
  - AWS Secrets Manager
  - Systems Manager Parameter Store
  - Patterns rotation và phương pháp hay nhất

- **Quản Trị Dữ Liệu:**
  - Chiến lược phân loại dữ liệu
  - Access guardrails và policies

---

**⭐ Trụ Cột 5 — Incident Response**

**11:10 – 11:40 AM | IR Playbook & Automation**
*Diễn giả: Mende Grabski (Long), Tĩnh Trường*

- **Chu Trình Incident Response:**
  - Chuẩn bị
  - Phát hiện & Phân tích
  - Ngăn chặn
  - Loại bỏ & Phục hồi
  - Hoạt động sau sự cố

- **IR Playbooks:**
  - Xử lý IAM key bị xâm phạm
  - Khắc phục S3 public exposure
  - Xử lý phát hiện malware trên EC2

- **Tự Động Hóa:**
  - Snapshot và thu thập bằng chứng
  - Quy trình cô lập instance
  - Auto-response với Lambda/Step Functions

---

**11:40 – 12:00 PM | Tổng Kết & Q&A**

- **Tóm Tắt 5 Trụ Cột Bảo Mật**
- **Những Lỗi Thường Gặp:**
  - Thách thức thực tế tại doanh nghiệp Việt Nam
  - Bài học từ các sự cố bảo mật

- **Lộ Trình Học Bảo Mật:**
  - AWS Certified Security – Specialty
  - AWS Certified Solutions Architect – Professional
  - Tài nguyên học tập liên tục

---

## Bài Học Rút Ra

**Nền Tảng Bảo Mật:**
- Bảo mật là trách nhiệm chung giữa AWS và khách hàng
- Triển khai defense in depth với nhiều lớp bảo mật
- Phương pháp Zero Trust: xác minh rõ ràng, sử dụng least privilege, giả định vi phạm

**Phương Pháp Hay Nhất IAM:**
- Loại bỏ long-term credentials, sử dụng temporary credentials
- Triển khai MFA cho tất cả người dùng
- Sử dụng IAM roles cho ứng dụng và dịch vụ
- Đánh giá truy cập thường xuyên với Access Analyzer

**Detection & Monitoring:**
- Bật CloudTrail ở cấp tổ chức
- Triển khai GuardDuty để phát hiện mối đe dọa
- Tập trung phát hiện bảo mật với Security Hub
- Tự động hóa phản ứng với sự kiện bảo mật

**Bảo Vệ Hạ Tầng:**
- Thiết kế VPC với phân đoạn phù hợp
- Sử dụng Security Groups làm firewall chính
- Triển khai WAF cho ứng dụng web
- Bảo vệ workload với các kiểm soát bảo mật phù hợp

**Bảo Vệ Dữ Liệu:**
- Mã hóa dữ liệu at rest và in transit
- Sử dụng KMS cho quản lý khóa tập trung
- Rotate secrets thường xuyên với Secrets Manager
- Phân loại dữ liệu và áp dụng kiểm soát phù hợp

**Incident Response:**
- Chuẩn bị IR playbooks trước khi sự cố xảy ra
- Tự động hóa hành động phản ứng khi có thể
- Thực hành quy trình incident response
- Học hỏi từ sự cố và cải thiện

**Bối Cảnh Doanh Nghiệp Việt Nam:**
- Thách thức bảo mật phổ biến tại thị trường địa phương
- Yêu cầu tuân thủ (PDPA, quy định địa phương)
- Chiến lược triển khai bảo mật tiết kiệm chi phí

---

## Hình Ảnh Sự Kiện

![AWS Well-Architected Security Pillar Workshop](/images/4-EvenPartcipated/event-5.jpg)

---

**Đánh giá:** ⭐⭐⭐⭐⭐ (5/5)

> Workshop bảo mật toàn diện và thực tế bao gồm tất cả các khía cạnh thiết yếu của bảo mật AWS với các tình huống thực tế phù hợp với doanh nghiệp Việt Nam.

