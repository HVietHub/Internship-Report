---
title: "Blog 3"
date: 2025-09-09
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# Bắt đầu với AWS Training dành cho phát triển game

Bởi Srividhya Pallay, Kenneth Tang, và Michael Orman — 03/03/2025

Danh mục: Amazon Aurora, Amazon DynamoDB, Amazon EC2, Amazon Elastic Kubernetes Service (Amazon EKS), Amazon GameLift, Amazon Kinesis, Amazon Redshift, Amazon Simple Storage Service (Amazon S3), AWS Training and Certification, Best Practices, Game Development, Games, Technical How-to

---

Các nhà phát triển game thường đối mặt với thách thức chung: xây dựng chuyên môn về cloud trong khi vẫn tập trung vào việc tạo ra trò chơi.

Các trò chơi hiện đại yêu cầu hạ tầng cloud tinh vi để phục vụ hàng triệu người chơi đồng thời, quản lý trải nghiệm multiplayer theo thời gian thực, và mang lại gameplay toàn cầu liền mạch — khiến chuyên môn về cloud trở thành yếu tố then chốt trong phát triển game.

Bài viết này tổng hợp các tài nguyên đào tạo quan trọng từ các khóa học cơ bản đến workshop thực hành và chứng chỉ được công nhận trong ngành. Dù bạn là:

- Một nhà phát triển độc lập đang muốn mở rộng trò chơi đầu tiên
- Một technical artist cần giải pháp render dựa trên cloud
- Hay một studio engineer thiết kế hạ tầng game quy mô lớn

→ Bạn đều sẽ tìm thấy lộ trình học tập phù hợp cho từng giai đoạn trong hành trình phát triển game trên cloud.

---

## Bài viết này bao gồm 4 phần chính:

1. **AWS Skill Builder training** qua các khóa học số và labs thực hành
2. **AWS Workshops và AWS Immersion Days** để triển khai giải pháp game thực tế
3. **AWS Certifications** phù hợp với nhu cầu công nghệ game
4. **Additional resources** gồm GitHub repositories, architecture guides, và các chương trình đào tạo

---

## AWS Skill Builder Trainings

Trong AWS Skill Builder, nội dung AWS for Games cung cấp bộ khóa học số và đào tạo thực hành toàn diện. Các khóa học trải dài từ kiến thức cơ bản về AWS đến dịch vụ AWS chuyên biệt cho game, phù hợp cả với người mới và người có kỹ năng nâng cao.

### Lộ trình khuyến nghị:

**Getting Started with AWS for Games – Part I**  
→ Khóa học nền tảng, giới thiệu tổng quan về các dịch vụ AWS liên quan đến game và lợi ích của AWS Cloud trong ngành game

**Getting Started with AWS for Games – Part II**  
→ Nâng cao: quản lý game server, phân tích game & quản lý người chơi, LiveOps, best practices về hạ tầng

**Game Server Hosting on AWS**  
→ Thảo luận về hosting game server trên AWS Cloud, gồm hiệu năng, vận hành, bảo mật; nhấn mạnh Amazon GameLift

**AWS for Games Containers Principles**  
→ Sử dụng Amazon ECS & Amazon EKS để chạy kiến trúc microservices cho game

**AWS for Games Learning Plan: Databases**  
→ Tích hợp database & storage với DynamoDB, Aurora, ElastiCache Serverless, S3, Kinesis trong nhiều thể loại game

**AWS for Games Learning Plan: Game Servers**  
→ Bộ 5 khóa học: từ khái niệm server cơ bản → nâng cao (GameLift, containerization, multi-region, DevOps)

**Cloud Game Development – Knowledge Badge Readiness Path**  
→ 9 khóa học bao phủ toàn bộ pipeline phát triển game; chứng thực chuyên môn xây dựng môi trường game trên cloud AWS

---

## AWS Workshops

Đối với những ai thích học qua trải nghiệm thực tế, AWS Workshops mang đến môi trường thực hành theo hướng dẫn chi tiết.

### Amazon GameLift Workshops

**Start a Full Stack Game with GameLift**  
→ Xây dựng multiplayer, session-based game với FlexMatch & FleetIQ

**AWS Game Tech Workshop**  
→ GameLift + build farm tối ưu cho Unity/Unreal

**AWS Game Backend Framework Workshop**  
→ Backend linh hoạt cho Unreal, Unity, Godot

**Amazon GameLift Testing Toolkit Workshop**  
→ Thực hành triển khai & sử dụng Testing Toolkit

### Database & Analytics Workshops

**Online Game Inventory with Amazon Serverless**  
→ Dùng Aurora Serverless, Lambda, API Gateway để xây inventory system

**Amazon Redshift for Gaming Industry**  
→ Phân tích dữ liệu người chơi gần thời gian thực với Redshift

**Analytics & AI/ML for Games**  
→ Xây dựng analytics pipeline để cải thiện engagement

### Ideation Workshops

**Upgrade Your Game Storyboard with Multi-Modal Prompt Chaining**  
→ Dùng Generative AI để phát triển storyboard từ ý tưởng ban đầu

---

## AWS Certifications

AWS Certifications xác nhận chuyên môn kỹ thuật trong các lĩnh vực cloud. Đây là chứng chỉ được công nhận rộng rãi, chứng minh khả năng thiết kế & triển khai giải pháp trên AWS.

Đối với game developers, 3 chứng chỉ sau đặc biệt phù hợp:

### AWS Certified Solutions Architect – Associate

→ Chứng chỉ nền tảng: thiết kế hệ thống phân tán trên AWS (EC2, S3, VPC, RDS, Route 53, IAM, v.v.)

### AWS Certified Developer – Associate

→ Tập trung vào dịch vụ phổ biến với game: Lambda, DynamoDB, API Gateway, S3, CloudFormation; CI/CD, serverless, containerization

### AWS Certified Advanced Networking – Specialty

→ Thiết kế & triển khai network phức tạp: VPC design, Transit Gateway, Direct Connect, Global Accelerator, Route 53; tập trung low-latency multiplayer, matchmaking toàn cầu, đồng bộ dữ liệu thời gian thực

---

## Additional Resources

**AWS Game Tech GitHub**  
→ Mẫu server, reference architectures, tích hợp Unity/Unreal, tutorials chi tiết

**AWS Game Tech Resources**  
→ Case studies, whitepapers, webinars, videos

**AWS for Games Blog**  
→ Bài viết chuyên sâu kỹ thuật, customer success stories, performance optimization

**AWS Educate**  
→ Khóa học cloud miễn phí cho sinh viên, lộ trình học game-focused, cộng đồng kết nối

---

## Conclusion

Tương lai của game nằm ở cloud.

Với AWS Training and Certification, bạn có thể xây dựng kỹ năng cần thiết để thiết kế, phát triển, và mở rộng trò chơi hiện đại trên cloud.

Bắt đầu hành trình học tập ngay hôm nay và gia nhập cộng đồng game developers am hiểu cloud, những người đang xây dựng thế hệ game tiếp theo trên AWS.

---


