---
title: "Configure Infrastructure Stacks"
date: 2025-12-01
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

#### Overview

Trong bước này, bạn sẽ cấu hình các CDK stacks cho infrastructure của EveryoneCook. Chúng ta sẽ cấu hình 7 stacks theo thứ tự dependency.

#### Project Structure

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
│   │   └── environment.ts      # Environment configuration
│   ├── cdk.json
│   ├── package.json
│   └── tsconfig.json
├── services/                    # Lambda functions
├── shared/                      # Shared code
└── .env                        # Environment variables
```

#### Step 1: Configure Environment Variables

**1. Copy Example File**

```bash
# Copy .env.example to .env
cp .env.example .env
```

**2. Edit .env File**

```bash
# Open .env in your editor
code .env  # or vim .env, nano .env
```

**3. Update Key Variables**

```bash
# AWS Configuration
AWS_REGION=us-east-1
AWS_ACCOUNT_ID=123456789012  # Your AWS account ID
AWS_PROFILE=default

# Environment
ENVIRONMENT=dev
NODE_ENV=development

# Domain Configuration
DOMAIN_NAME=everyonecook.cloud
API_DOMAIN=api.everyonecook.cloud
CDN_DOMAIN=cdn.everyonecook.cloud
FRONTEND_URL=https://everyonecook.cloud
API_URL=https://api.everyonecook.cloud

# OpenSearch (optional for dev)
ENABLE_OPENSEARCH=false  # Set to true if you want OpenSearch in dev

# GitLab Configuration
GITLAB_REPO=your-username/everyonecook
GITLAB_BRANCH=main
GITLAB_TOKEN=your-gitlab-token

# Email Configuration
SES_FROM_EMAIL=noreply@everyonecook.cloud
ALERT_EMAIL=your-email@example.com
```

![Environment Variables](/images/5-Workshop/5.4-configure-stacks/env-file.png)
*Screenshot: .env file with configuration*

#### Step 2: Review Stack Configuration

**1. Check Environment Config**

```bash
# Navigate to infrastructure directory
cd infrastructure

# View environment configuration
cat config/environment.ts
```

Cấu hình này định nghĩa:
- Domain names cho từng environment (dev, staging, prod)
- AWS account và region
- Feature flags (OpenSearch, etc.)
- Contact information

**2. Review CDK App Entry Point**

```bash
# View CDK app configuration
cat bin/app.ts
```

File này:
- Import tất cả các stacks
- Tạo stack instances với dependencies
- Apply tags cho tất cả resources

#### Step 3: Understand Stack Dependencies

Các stacks phải deploy theo thứ tự vì có dependencies:

```
1. DNS Stack (Route 53)
   ↓
2. Certificate Stack (ACM - depends on DNS)
   ↓
3. Core Stack (DynamoDB, S3, CloudFront, OpenSearch - depends on Certificate)
   ↓
4. Auth Stack (Cognito, SES - depends on Core)
   ↓
5. Backend Stack (API Gateway, Lambda - depends on Auth)
   ↓
6. Frontend Stack (Amplify - depends on Backend) [Optional]
   ↓
7. Observability Stack (CloudWatch, X-Ray - depends on all)
```

#### Step 4: Configure Each Stack

**1. DNS Stack Configuration**

```typescript
// infrastructure/lib/stacks/dns-stack.ts
// Tạo Route 53 Hosted Zone cho domain
```

**Key Configuration:**
- Domain name: `everyonecook.cloud`
- Hosted zone type: Public
- Export nameservers để cấu hình ở domain registrar

**2. Certificate Stack Configuration**

```typescript
// infrastructure/lib/stacks/certificate-stack.ts
// Tạo ACM certificates cho CloudFront và API Gateway
```

**Key Configuration:**
- CloudFront certificate: `cdn.everyonecook.cloud`
- API Gateway certificate: `*.everyonecook.cloud` (wildcard)
- Region: us-east-1 (required for CloudFront)
- Validation: DNS via Route 53

**3. Core Stack Configuration**

```typescript
// infrastructure/lib/stacks/core-stack.ts
// DynamoDB, S3, CloudFront, KMS, OpenSearch
```

**Key Configuration:**
- DynamoDB: Single Table Design, username-based PK
- S3: 4 buckets (Content, Logs, Emails, CDN Logs)
- CloudFront: OAC, Shield Standard
- OpenSearch: Conditional (enabled if ENABLE_OPENSEARCH=true)
- KMS: 2 keys (DynamoDB, S3)

**4. Auth Stack Configuration**

```typescript
// infrastructure/lib/stacks/auth-stack.ts
// Cognito, SES, Lambda triggers
```

**Key Configuration:**
- Cognito: Username/email sign-in, NO MFA
- Lambda triggers: 5 triggers (Pre-signup, Post-confirmation, etc.)
- SES: Email identity với DKIM
- Password policy: Min 12 chars

**5. Backend Stack Configuration**

```typescript
// infrastructure/lib/stacks/backend-stack.ts
// API Gateway, Lambda, SQS, WAF
```

**Key Configuration:**
- API Gateway: REST API với Cognito authorizer
- Lambda: 6 modules + 1 worker
- SQS: 6 queues + 6 DLQs
- WAF: API Gateway only (CloudFront uses Shield Standard)

**6. Frontend Stack Configuration (Optional)**

```typescript
// infrastructure/lib/stacks/frontend-stack.ts
// AWS Amplify hosting
```

**Key Configuration:**
- Amplify: Next.js 15 app
- GitLab integration
- Custom domain
- Auto-deploy on push

**7. Observability Stack Configuration**

```typescript
// infrastructure/lib/stacks/observability-stack.ts
// CloudWatch, X-Ray
```

**Key Configuration:**
- Dashboards: 4 dashboards (Core, Auth, Backend, Overview)
- Alarms: Critical + Warning
- SNS: Email notifications
- X-Ray: Distributed tracing

#### Step 5: Validate Configuration

**1. Check TypeScript Compilation**

```bash
# Navigate to infrastructure directory
cd infrastructure

# Compile TypeScript
npm run build

# Should complete without errors
```

![TypeScript Build Success](/images/5-Workshop/5.4-configure-stacks/typescript-build.png)
*Screenshot: Terminal showing successful TypeScript compilation*

**2. Validate CDK Syntax**

```bash
# List all stacks
npx cdk list --context environment=dev

# Should show:
# EveryoneCook-dev-DNS
# EveryoneCook-dev-Certificate
# EveryoneCook-dev-Core
# EveryoneCook-dev-Auth
# EveryoneCook-dev-Backend
# EveryoneCook-dev-Observability
```

![CDK List Stacks](/images/5-Workshop/5.4-configure-stacks/cdk-list.png)
*Screenshot: Terminal showing all CDK stacks listed*

**3. Synthesize CloudFormation Templates**

```bash
# Synthesize all stacks (generate CloudFormation templates)
npx cdk synth --context environment=dev

# This creates cdk.out/ directory with CloudFormation templates
```

**4. Check Generated Templates**

```bash
# List generated templates
ls -la cdk.out/

# Should show:
# - EveryoneCook-dev-DNS.template.json
# - EveryoneCook-dev-Certificate.template.json
# - EveryoneCook-dev-Core.template.json
# - EveryoneCook-dev-Auth.template.json
# - EveryoneCook-dev-Backend.template.json
# - EveryoneCook-dev-Observability.template.json
```

#### Step 6: Review Stack Resources

**1. DNS Stack Resources**

```bash
# View DNS stack template
cat cdk.out/EveryoneCook-dev-DNS.template.json | jq '.Resources | keys'
```

Resources:
- Route 53 Hosted Zone
- NS records
- SOA record

**2. Certificate Stack Resources**

```bash
# View Certificate stack template
cat cdk.out/EveryoneCook-dev-Certificate.template.json | jq '.Resources | keys'
```

Resources:
- 2 ACM Certificates (CloudFront + API Gateway)
- DNS validation records

**3. Core Stack Resources**

```bash
# View Core stack template
cat cdk.out/EveryoneCook-dev-Core.template.json | jq '.Resources | keys'
```

Resources:
- DynamoDB table + 5 GSI indexes
- 4 S3 buckets
- CloudFront distribution
- 2 KMS keys
- OpenSearch domain (if enabled)

**4. Auth Stack Resources**

```bash
# View Auth stack template
cat cdk.out/EveryoneCook-dev-Auth.template.json | jq '.Resources | keys'
```

Resources:
- Cognito User Pool
- 5 Lambda triggers
- SES email identity
- IAM roles

**5. Backend Stack Resources**

```bash
# View Backend stack template
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
# View Observability stack template
cat cdk.out/EveryoneCook-dev-Observability.template.json | jq '.Resources | keys'
```

Resources:
- 4 CloudWatch dashboards
- CloudWatch alarms
- SNS topics
- Composite alarm

#### Step 7: Estimate Costs

**Development Environment (without OpenSearch):**
- DynamoDB: $3-5/month
- S3: $1-3/month
- CloudFront: $2-5/month
- Lambda: $0-3/month
- API Gateway: $0-3/month
- Cognito: $0/month (free tier)
- SES: $0-1/month
- Route 53: $0.50/month
- CloudWatch: $2-5/month
- WAF: $5-8/month

**Total: $15-35/month**

**With OpenSearch:**
- Add $50-100/month for OpenSearch domain

**Total with OpenSearch: $65-135/month**

#### Troubleshooting

**Issue: TypeScript compilation errors**

```bash
# Clean and rebuild
cd infrastructure
rm -rf node_modules dist
npm install
npm run build
```

**Issue: CDK synth fails**

```bash
# Check for syntax errors
npx cdk synth --context environment=dev --verbose

# Check environment variables
cat ../.env
```

**Issue: Missing dependencies**

```bash
# Install all dependencies
cd infrastructure
npm install

# Install root dependencies
cd ..
npm install
```

**Issue: Wrong AWS account**

```bash
# Verify AWS account
aws sts get-caller-identity

# Should match AWS_ACCOUNT_ID in .env
```

#### Configuration Checklist

- [ ] .env file created and configured
- [ ] AWS_ACCOUNT_ID matches your account
- [ ] DOMAIN_NAME set correctly
- [ ] ENABLE_OPENSEARCH set (true/false)
- [ ] TypeScript compiles without errors
- [ ] CDK list shows all 6-7 stacks
- [ ] CDK synth generates templates
- [ ] No syntax errors in stack code

#### Next Steps

Once configuration is complete and validated, proceed to [Deploy Infrastructure](../5.5-deploy-infrastructure/) to deploy all stacks to AWS.
