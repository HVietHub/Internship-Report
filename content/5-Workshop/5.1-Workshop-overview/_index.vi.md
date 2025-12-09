---
title: "Tổng quan Workshop"
date: 2025-12-01
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

#### Kiến trúc EveryoneCook

**EveryoneCook** là một nền tảng nấu ăn xã hội hiện đại được xây dựng hoàn toàn trên các công nghệ serverless của AWS. Kiến trúc tuân theo các best practices về khả năng mở rộng, bảo mật và tối ưu chi phí, với trọng tâm hỗ trợ nguyên liệu tiếng Việt và gợi ý công thức nấu ăn bằng AI.

#### Các thành phần chính

+ **Frontend**: Ứng dụng Next.js 15 với Flowbite React components được host trên AWS Amplify
+ **Backend**: Serverless API sử dụng API Gateway với API Router pattern và 6 Lambda modules
+ **Database**: DynamoDB Single Table Design với username-based PK và 5 GSI indexes
+ **Search**: OpenSearch với Vietnamese analyzer cho tìm kiếm công thức và bài đăng nâng cao
+ **Storage**: S3 buckets với Intelligent-Tiering và CloudFront CDN với OAC
+ **Authentication**: Cognito User Pool với Lambda triggers cho custom flows
+ **Email**: SES với DKIM signing cho transactional emails
+ **AI Integration**: Amazon Bedrock (Claude 3.5 Sonnet v2) với dictionary-first translation
+ **Security**: WAF cho API Gateway, Shield Standard cho CloudFront, KMS encryption
+ **Monitoring**: CloudWatch dashboards, alarms, và X-Ray distributed tracing

#### Sơ đồ kiến trúc

```
                    ┌─────────────────────────────────┐
                    │      Route 53 DNS               │
                    │   everyonecook.cloud            │
                    └──────┬──────────────┬───────────┘
                           │              │
                ┌──────────▼──────┐  ┌───▼──────────────┐
                │  CloudFront CDN │  │ Amplify Hosting  │
                │  cdn.*          │  │ everyonecook.*   │
                │  (Shield Std)   │  │ Next.js 15       │
                └──────┬──────────┘  └──────────────────┘
                       │ OAC
                ┌──────▼──────────────────────────────┐
                │      S3 Buckets (4)                 │
                │  - Content (avatars, posts)         │
                │  - Logs (CloudWatch archive)        │
                │  - Emails (SES incoming)            │
                │  - CDN Logs (CloudFront)            │
                │  Intelligent-Tiering + KMS          │
                └─────────────────────────────────────┘

┌────────────────────────────────────────────────────────────────┐
│                    API Gateway (api.*)                          │
│              Cognito Authorizer + WAF                           │
└──────────────────────────┬─────────────────────────────────────┘
                           │
                  ┌────────▼────────┐
                  │   API Router    │
                  │   Lambda        │
                  └────────┬────────┘
                           │
        ┌──────────────────┼──────────────────┐
        │                  │                  │
   ┌────▼────┐      ┌─────▼─────┐     ┌─────▼─────┐
   │  Auth   │      │  Social   │     │  Recipe   │
   │  Module │      │  Module   │     │  + AI     │
   └────┬────┘      └─────┬─────┘     └─────┬─────┘
        │                  │                  │
   ┌────▼────┐      ┌─────▼─────┐           │
   │  Admin  │      │  Upload   │           │
   │  Module │      │  Module   │           │
   └────┬────┘      └─────┬─────┘           │
        │                  │                  │
        └──────────────────┼──────────────────┘
                           │
        ┌──────────────────┼──────────────────┐
        │                  │                  │
   ┌────▼────────┐   ┌────▼────────┐   ┌────▼────────┐
   │  DynamoDB   │   │  OpenSearch │   │  Bedrock AI │
   │  Single     │   │  Vietnamese │   │  Claude 3.5 │
   │  Table      │   │  Analyzer   │   │  Sonnet v2  │
   └─────────────┘   └─────────────┘   └─────────────┘

┌────────────────────────────────────────────────────────────────┐
│                    SQS Queues (6)                               │
│  AI | Email | SearchIndex | Image | Analytics | Notification   │
└──────────────────────────┬─────────────────────────────────────┘
                           │
                  ┌────────▼────────┐
                  │  Search Sync    │
                  │  Worker Lambda  │
                  └─────────────────┘

┌────────────────────────────────────────────────────────────────┐
│              Cognito User Pool + 5 Lambda Triggers              │
│  Post-Confirmation | Pre-Auth | Post-Auth | Custom-Msg | Pre-Signup │
└────────────────────────────┬───────────────────────────────────┘
                             │
                    ┌────────▼────────┐
                    │   SES Email     │
                    │   DKIM + SPF    │
                    └─────────────────┘

┌────────────────────────────────────────────────────────────────┐
│         CloudWatch Dashboards + Alarms + X-Ray Tracing          │
└────────────────────────────────────────────────────────────────┘
```

#### Luồng Workshop

Workshop này tuân theo quy trình phát triển ứng dụng thực tế:

1. **Cài đặt môi trường** - Cài đặt các công cụ (Node.js, AWS CLI, CDK CLI)
2. **CDK Bootstrap** - Chuẩn bị tài khoản AWS cho CDK deployments
3. **Cấu hình Stacks** - Thiết lập cấu hình infrastructure (DNS, Certificate, Core, Auth, Backend, Observability)
4. **Deploy Infrastructure** - Deploy tất cả CDK stacks lên AWS
5. **Cấu hình API & Lambda** - Thiết lập API Gateway routes và Lambda functions
6. **Deploy Backend** - Deploy API và Lambda code
7. **Test Endpoints** - Xác minh tất cả endpoints hoạt động end-to-end
8. **Push lên GitLab** - Version control và thiết lập CI/CD
9. **Deploy lên Amplify** - Deploy frontend lên AWS Amplify
10. **Giám sát & Bảo trì** - Sử dụng CloudWatch và X-Ray để monitoring

#### Những gì bạn sẽ học

+ Infrastructure as Code với AWS CDK (TypeScript)
+ Kiến trúc Serverless với API Router pattern
+ DynamoDB Single Table Design với username-based PK
+ OpenSearch với Vietnamese language analyzer
+ CloudFront CDN với Origin Access Control (OAC)
+ Cognito authentication với Lambda triggers
+ Tổ chức Lambda function theo module (6 modules)
+ Xử lý bất đồng bộ dựa trên SQS với workers
+ Tích hợp Bedrock AI với caching strategy
+ Bảo mật WAF cho API Gateway
+ CloudWatch monitoring và X-Ray tracing
+ Chiến lược tối ưu chi phí ($15-24/ngày cho 10K users)

#### Tính năng chính

+ **Hỗ trợ tiếng Việt**: OpenSearch Vietnamese analyzer cho tìm kiếm nguyên liệu
+ **AI-Powered**: Bedrock Claude 3.5 Sonnet v2 cho tạo công thức
+ **Dictionary-First Translation**: Mục tiêu 99% coverage với intelligent caching
+ **Nền tảng xã hội**: Posts, comments, reactions, friends, notifications
+ **Field-Level Privacy**: Kiểm soát chi tiết về hiển thị profile
+ **Content Moderation**: Quy trình kiểm duyệt tự động và thủ công
+ **Tìm kiếm nâng cao**: Full-text search với Vietnamese normalization
+ **Tối ưu chi phí**: Intelligent-Tiering, caching, và resource optimization
