---
title: "Cấu hình Infrastructure Stacks"
date: 2025-12-01
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

#### Tổng quan

Trong bước này, bạn sẽ cấu hình các CDK stacks cho infrastructure của EveryoneCook. Chúng ta sẽ cấu hình 7 stacks theo thứ tự dependency.

#### Cấu trúc Project

```
everyonecook-dev/
├── infrastructure/
│   ├── bin/
│   │   └── app.ts              # CDK app entry point
│   ├── lib/
│   │   ├── base-stack.ts       # Base stack class
│   │   └── stacks/
│   │       ├── dns-stack.ts
│   │       ├── certificate-stack.ts
│   │       ├── core-stack.ts
│   │       ├── auth-stack.ts
│   │       ├── backend-stack.ts
│   │       ├── frontend-stack.ts
│   │       └── observability-stack.ts
│   ├── config/
│   │   └── environment.ts      # Cấu hình môi trường
│   ├── cdk.json
│   ├── package.json
│   └── tsconfig.json
├── services/                    # Lambda functions
├── shared/                      # Code dùng chung
└── .env                        # Biến môi trường
```

#### Bước 1: Cấu hình biến môi trường

**1. Sao chép file mẫu**

```bash
# Sao chép .env.example sang .env
cp .env.example .env
```

**2. Chỉnh sửa file .env**

```bash
# Mở .env trong trình soạn thảo
code .env  # hoặc vim .env, nano .env
```

**3. Cập nhật các biến quan trọng**

```bash
# Cấu hình AWS
AWS_REGION=us-east-1
AWS_ACCOUNT_ID=123456789012  # AWS account ID của bạn
AWS_PROFILE=default

# Môi trường
ENVIRONMENT=dev
NODE_ENV=development

# Cấu hình Domain
DOMAIN_NAME=everyonecook.cloud
API_DOMAIN=api.everyonecook.cloud
CDN_DOMAIN=cdn.everyonecook.cloud
FRONTEND_URL=https://everyonecook.cloud
API_URL=https://api.everyonecook.cloud

# OpenSearch (tùy chọn cho dev)
ENABLE_OPENSEARCH=false  # Đặt true nếu muốn OpenSearch trong dev

# Cấu hình GitLab
GITLAB_REPO=your-username/everyonecook
GITLAB_BRANCH=main
GITLAB_TOKEN=your-gitlab-token

# Cấu hình Email
SES_FROM_EMAIL=noreply@everyonecook.cloud
ALERT_EMAIL=your-email@example.com
```

![Biến môi trường](/images/5-Workshop/5.4-configure-stacks/env-file.png)
*Ảnh chụp: File .env với cấu hình*

#### Bước 2: Xem xét cấu hình Stack

**1. Kiểm tra cấu hình môi trường**

```bash
# Di chuyển đến thư mục infrastructure
cd infrastructure

# Xem cấu hình môi trường
cat config/environment.ts
```

Cấu hình này định nghĩa:
- Domain names cho từng environment (dev, staging, prod)
- AWS account và region
- Feature flags (OpenSearch, etc.)
- Thông tin liên hệ

**2. Xem xét CDK App Entry Point**

```bash
# Xem cấu hình CDK app
cat bin/app.ts
```

File này:
- Import tất cả các stacks
- Tạo stack instances với dependencies
- Apply tags cho tất cả resources

#### Bước 3: Hiểu về Stack Dependencies

Các stacks phải deploy theo thứ tự vì có dependencies:

```
1. DNS Stack (Route 53)
   ↓
2. Certificate Stack (ACM - phụ thuộc DNS)
   ↓
3. Core Stack (DynamoDB, S3, CloudFront, OpenSearch - phụ thuộc Certificate)
   ↓
4. Auth Stack (Cognito, SES - phụ thuộc Core)
   ↓
5. Backend Stack (API Gateway, Lambda - phụ thuộc Auth)
   ↓
6. Frontend Stack (Amplify - phụ thuộc Backend) [Tùy chọn]
   ↓
7. Observability Stack (CloudWatch, X-Ray - phụ thuộc tất cả)
```

#### Bước 4: Cấu hình từng Stack

**1. Cấu hình DNS Stack**

```typescript
// infrastructure/lib/stacks/dns-stack.ts
// Tạo Route 53 Hosted Zone cho domain
```

**Cấu hình chính:**
- Domain name: `everyonecook.cloud`
- Hosted zone type: Public
- Export nameservers để cấu hình ở domain registrar

**2. Cấu hình Certificate Stack**

```typescript
// infrastructure/lib/stacks/certificate-stack.ts
// Tạo ACM certificates cho CloudFront và API Gateway
```

**Cấu hình chính:**
- CloudFront certificate: `cdn.everyonecook.cloud`
- API Gateway certificate: `*.everyonecook.cloud` (wildcard)
- Region: us-east-1 (bắt buộc cho CloudFront)
- Validation: DNS qua Route 53

**3. Cấu hình Core Stack**

```typescript
// infrastructure/lib/stacks/core-stack.ts
// DynamoDB, S3, CloudFront, KMS, OpenSearch
```

**Cấu hình chính:**
- DynamoDB: Single Table Design, username-based PK
- S3: 4 buckets (Content, Logs, Emails, CDN Logs)
- CloudFront: OAC, Shield Standard
- OpenSearch: Có điều kiện (bật nếu ENABLE_OPENSEARCH=true)
- KMS: 2 keys (DynamoDB, S3)

**4. Cấu hình Auth Stack**

```typescript
// infrastructure/lib/stacks/auth-stack.ts
// Cognito, SES, Lambda triggers
```

**Cấu hình chính:**
- Cognito: Đăng nhập bằng Username/email, KHÔNG có MFA
- Lambda triggers: 5 triggers (Pre-signup, Post-confirmation, etc.)
- SES: Email identity với DKIM
- Password policy: Tối thiểu 12 ký tự

**5. Cấu hình Backend Stack**

```typescript
// infrastructure/lib/stacks/backend-stack.ts
// API Gateway, Lambda, SQS, WAF
```

**Cấu hình chính:**
- API Gateway: REST API với Cognito authorizer
- Lambda: 6 modules + 1 worker
- SQS: 6 queues + 6 DLQs
- WAF: Chỉ cho API Gateway (CloudFront sử dụng Shield Standard)

**6. Cấu hình Frontend Stack (Tùy chọn)**

```typescript
// infrastructure/lib/stacks/frontend-stack.ts
// AWS Amplify hosting
```

**Cấu hình chính:**
- Amplify: Next.js 15 app
- Tích hợp GitLab
- Custom domain
- Auto-deploy khi push

**7. Cấu hình Observability Stack**

```typescript
// infrastructure/lib/stacks/observability-stack.ts
// CloudWatch, X-Ray
```

**Cấu hình chính:**
- Dashboards: 4 dashboards (Core, Auth, Backend, Overview)
- Alarms: Critical + Warning
- SNS: Email notifications
- X-Ray: Distributed tracing

#### Bước 5: Xác thực cấu hình

**1. Kiểm tra TypeScript Compilation**

```bash
# Di chuyển đến thư mục infrastructure
cd infrastructure

# Compile TypeScript
npm run build

# Phải hoàn thành không có lỗi
```

![TypeScript Build thành công](/images/5-Workshop/5.4-configure-stacks/typescript-build.png)
*Ảnh chụp: Terminal hiển thị TypeScript compilation thành công*

**2. Xác thực CDK Syntax**

```bash
# Liệt kê tất cả stacks
npx cdk list --context environment=dev

# Phải hiển thị:
# EveryoneCook-dev-DNS
# EveryoneCook-dev-Certificate
# EveryoneCook-dev-Core
# EveryoneCook-dev-Auth
# EveryoneCook-dev-Backend
# EveryoneCook-dev-Observability
```

![CDK List Stacks](/images/5-Workshop/5.4-configure-stacks/cdk-list.png)
*Ảnh chụp: Terminal hiển thị tất cả CDK stacks*

**3. Tổng hợp CloudFormation Templates**

```bash
# Tổng hợp tất cả stacks (tạo CloudFormation templates)
npx cdk synth --context environment=dev

# Lệnh này tạo thư mục cdk.out/ với CloudFormation templates
```

**4. Kiểm tra Templates đã tạo**

```bash
# Liệt kê templates đã tạo
ls -la cdk.out/

# Phải hiển thị:
# - EveryoneCook-dev-DNS.template.json
# - EveryoneCook-dev-Certificate.template.json
# - EveryoneCook-dev-Core.template.json
# - EveryoneCook-dev-Auth.template.json
# - EveryoneCook-dev-Backend.template.json
# - EveryoneCook-dev-Observability.template.json
```

#### Bước 6: Xem xét Stack Resources

**1. DNS Stack Resources**

```bash
# Xem DNS stack template
cat cdk.out/EveryoneCook-dev-DNS.template.json | jq '.Resources | keys'
```

Resources:
- Route 53 Hosted Zone
- NS records
- SOA record

**2. Certificate Stack Resources**

```bash
# Xem Certificate stack template
cat cdk.out/EveryoneCook-dev-Certificate.template.json | jq '.Resources | keys'
```

Resources:
- 2 ACM Certificates (CloudFront + API Gateway)
- DNS validation records

**3. Core Stack Resources**

```bash
# Xem Core stack template
cat cdk.out/EveryoneCook-dev-Core.template.json | jq '.Resources | keys'
```

Resources:
- DynamoDB table + 5 GSI indexes
- 4 S3 buckets
- CloudFront distribution
- 2 KMS keys
- OpenSearch domain (nếu bật)

**4. Auth Stack Resources**

```bash
# Xem Auth stack template
cat cdk.out/EveryoneCook-dev-Auth.template.json | jq '.Resources | keys'
```

Resources:
- Cognito User Pool
- 5 Lambda triggers
- SES email identity
- IAM roles

**5. Backend Stack Resources**

```bash
# Xem Backend stack template
cat cdk.out/EveryoneCook-dev-Backend.template.json | jq '.Resources | keys'
```

Resources:
- API Gateway REST API
- 6 Lambda functions + 1 worker
- 6 SQS queues + 6 DLQs
- WAF Web ACL
- IAM roles

**6. Observability Stack Resources**

```bash
# Xem Observability stack template
cat cdk.out/EveryoneCook-dev-Observability.template.json | jq '.Resources | keys'
```

Resources:
- 4 CloudWatch dashboards
- CloudWatch alarms
- SNS topics
- Composite alarm

#### Bước 7: Ước tính chi phí

**Môi trường Development (không có OpenSearch):**
- DynamoDB: $3-5/tháng
- S3: $1-3/tháng
- CloudFront: $2-5/tháng
- Lambda: $0-3/tháng
- API Gateway: $0-3/tháng
- Cognito: $0/tháng (free tier)
- SES: $0-1/tháng
- Route 53: $0.50/tháng
- CloudWatch: $2-5/tháng
- WAF: $5-8/tháng

**Tổng: $15-35/tháng**

**Với OpenSearch:**
- Thêm $50-100/tháng cho OpenSearch domain

**Tổng với OpenSearch: $65-135/tháng**

#### Xử lý sự cố

**Vấn đề: Lỗi TypeScript compilation**

```bash
# Dọn dẹp và build lại
cd infrastructure
rm -rf node_modules dist
npm install
npm run build
```

**Vấn đề: CDK synth thất bại**

```bash
# Kiểm tra lỗi syntax
npx cdk synth --context environment=dev --verbose

# Kiểm tra biến môi trường
cat ../.env
```

**Vấn đề: Thiếu dependencies**

```bash
# Cài đặt tất cả dependencies
cd infrastructure
npm install

# Cài đặt root dependencies
cd ..
npm install
```

**Vấn đề: Sai AWS account**

```bash
# Xác minh AWS account
aws sts get-caller-identity

# Phải khớp với AWS_ACCOUNT_ID trong .env
```

#### Checklist cấu hình

- [ ] File .env đã tạo và cấu hình
- [ ] AWS_ACCOUNT_ID khớp với account của bạn
- [ ] DOMAIN_NAME đặt đúng
- [ ] ENABLE_OPENSEARCH đã đặt (true/false)
- [ ] TypeScript compile không có lỗi
- [ ] CDK list hiển thị tất cả 6-7 stacks
- [ ] CDK synth tạo templates
- [ ] Không có lỗi syntax trong stack code

#### Bước tiếp theo

Sau khi cấu hình hoàn tất và xác thực, tiến hành [Deploy Infrastructure](../5.5-deploy-infrastructure/) để deploy tất cả stacks lên AWS.
