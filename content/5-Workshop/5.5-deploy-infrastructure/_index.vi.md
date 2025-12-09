---
title: "Deploy Infrastructure"
date: 2025-12-01
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

#### Tổng quan

Trong bước này, bạn sẽ deploy tất cả infrastructure stacks lên AWS theo đúng thứ tự dependencies. Quá trình này mất khoảng 30-45 phút.

#### Thứ tự Deployment

```
1. DNS Stack          (2-3 phút)
2. Certificate Stack  (5-10 phút - DNS validation)
3. Core Stack         (10-15 phút - CloudFront + OpenSearch)
4. Auth Stack         (5-7 phút - Cognito + SES)
5. Backend Stack      (8-12 phút - API Gateway + Lambda)
6. Observability Stack (3-5 phút - CloudWatch)
```

**Tổng thời gian: 30-50 phút**

#### Bước 1: Deploy DNS Stack

**1. Deploy Stack**

```bash
# Di chuyển đến thư mục infrastructure
cd infrastructure

# Deploy DNS stack
npx cdk deploy EveryoneCook-dev-DNS --context environment=dev

# Xem xét thay đổi và gõ 'y' để xác nhận
```

![Deploy DNS Stack](/images/5-Workshop/5.5-deploy-infrastructure/dns-deploy.png)
*Ảnh chụp: Terminal hiển thị deployment DNS stack*

**2. Lấy Nameservers**

Sau khi deploy xong, lưu lại 4 nameservers từ output:

```
Outputs:
EveryoneCook-dev-DNS.NameServers = ns-123.awsdns-12.com, ns-456.awsdns-45.net, ...
EveryoneCook-dev-DNS.HostedZoneId = Z1234567890ABC
```

**3. Cập nhật Domain Registrar**

Đi đến domain registrar (Hostinger, GoDaddy, etc.) và cập nhật nameservers:

1. Đăng nhập vào domain registrar
2. Tìm domain của bạn (everyonecook.cloud)
3. Chọn "Custom Nameservers"
4. Nhập 4 nameservers từ AWS
5. Lưu thay đổi

![Hostinger Nameservers](/images/5-Workshop/5.5-deploy-infrastructure/hostinger-nameservers.png)
*Ảnh chụp: Hostinger hiển thị nameservers đã cập nhật*

**4. Chờ DNS Propagation**

```bash
# Kiểm tra DNS propagation (có thể mất 5-30 phút)
dig NS everyonecook.cloud

# Hoặc dùng công cụ online: https://www.whatsmydns.net/
```

#### Bước 2: Deploy Certificate Stack

**Quan trọng:** Stack này phải deploy ở region us-east-1 (yêu cầu của CloudFront)

**1. Deploy Stack**

```bash
# Deploy Certificate stack
npx cdk deploy EveryoneCook-dev-Certificate --context environment=dev

# Gõ 'y' để xác nhận
```

**2. Chờ DNS Validation**

ACM sẽ tự động:
- Tạo CNAME records trong Route 53
- Xác thực quyền sở hữu domain
- Cấp certificates

Quá trình này mất 5-10 phút.

![Certificate Validation](/images/5-Workshop/5.5-deploy-infrastructure/certificate-validation.png)
*Ảnh chụp: ACM hiển thị certificates đang được xác thực*

**3. Xác minh Certificates**

```bash
# Kiểm tra trạng thái certificate
aws acm list-certificates --region us-east-1

# Cả hai certificates phải hiển thị Status: ISSUED
```

![Certificates đã cấp](/images/5-Workshop/5.5-deploy-infrastructure/certificates-issued.png)
*Ảnh chụp: ACM console hiển thị cả hai certificates đã được cấp*

#### Bước 3: Deploy Core Stack

**1. Deploy Stack**

```bash
# Deploy Core stack (mất 10-15 phút)
npx cdk deploy EveryoneCook-dev-Core --context environment=dev

# Gõ 'y' để xác nhận
```

Deployment bao gồm:
- Tạo DynamoDB table
- 4 S3 buckets
- CloudFront distribution (mất lâu nhất)
- 2 KMS keys
- OpenSearch domain (nếu bật)

![Deploy Core Stack](/images/5-Workshop/5.5-deploy-infrastructure/core-deploy.png)
*Ảnh chụp: Terminal hiển thị tiến trình deployment Core stack*

**2. Theo dõi Deployment**

```bash
# Trong terminal khác, theo dõi CloudFormation events
aws cloudformation describe-stack-events \
  --stack-name EveryoneCook-dev-Core \
  --max-items 10 \
  --query 'StackEvents[*].[Timestamp,ResourceStatus,ResourceType,LogicalResourceId]' \
  --output table
```

**3. Xác minh Resources**

```bash
# Kiểm tra DynamoDB table
aws dynamodb describe-table --table-name EveryoneCook-dev

# Kiểm tra S3 buckets
aws s3 ls | grep everyonecook

# Kiểm tra CloudFront distribution
aws cloudfront list-distributions --query 'DistributionList.Items[*].[Id,DomainName,Status]'
```

![Core Resources](/images/5-Workshop/5.5-deploy-infrastructure/core-resources.png)
*Ảnh chụp: AWS Console hiển thị Core stack resources*

#### Bước 4: Deploy Auth Stack

**1. Deploy Stack**

```bash
# Deploy Auth stack
npx cdk deploy EveryoneCook-dev-Auth --context environment=dev

# Gõ 'y' để xác nhận
```

Deployment bao gồm:
- Cognito User Pool
- 5 Lambda triggers
- SES email identity
- IAM roles

**2. Xác minh SES Email Identity**

```bash
# Kiểm tra trạng thái SES identity
aws sesv2 get-email-identity --email-identity everyonecook.cloud

# Phải hiển thị: VerificationStatus: SUCCESS
```

**3. Yêu cầu SES Production Access**

Nếu muốn gửi email thực:

1. Vào SES Console → Account dashboard
2. Nhấn "Request production access"
3. Điền form:
   - Use case: Transactional emails
   - Website: https://everyonecook.cloud
   - Mô tả xử lý bounce
4. Gửi (thường được duyệt trong 24 giờ)

![SES đã xác minh](/images/5-Workshop/5.5-deploy-infrastructure/ses-verified.png)
*Ảnh chụp: SES console hiển thị email identity đã xác minh*

#### Bước 5: Deploy Backend Stack

**1. Chuẩn bị Backend Code**

```bash
# Di chuyển đến project root
cd ..

# Build và chuẩn bị tất cả Lambda modules
.\prepare-backend-deployment.ps1

# Script này:
# - Compile TypeScript sang JavaScript
# - Cài đặt production dependencies
# - Tạo deployment packages
```

![Chuẩn bị Backend](/images/5-Workshop/5.5-deploy-infrastructure/backend-prep.png)
*Ảnh chụp: Terminal hiển thị chuẩn bị backend*

**2. Deploy Stack**

```bash
# Di chuyển lại infrastructure
cd infrastructure

# Deploy Backend stack
npx cdk deploy EveryoneCook-dev-Backend --context environment=dev

# Gõ 'y' để xác nhận
```

Deployment bao gồm:
- API Gateway REST API
- 6 Lambda modules + 1 worker
- 6 SQS queues + 6 DLQs
- WAF Web ACL

**3. Xác minh API Gateway**

```bash
# Lấy API endpoint
aws cloudformation describe-stacks \
  --stack-name EveryoneCook-dev-Backend \
  --query 'Stacks[0].Outputs[?OutputKey==`ApiEndpoint`].OutputValue' \
  --output text

# Test health endpoint
curl https://api.everyonecook.cloud/health
```

![Backend đã deploy](/images/5-Workshop/5.5-deploy-infrastructure/backend-deployed.png)
*Ảnh chụp: API Gateway console hiển thị REST API đã deploy*

#### Bước 6: Deploy Observability Stack

**1. Deploy Stack**

```bash
# Deploy Observability stack
npx cdk deploy EveryoneCook-dev-Observability --context environment=dev

# Gõ 'y' để xác nhận
```

Deployment bao gồm:
- 4 CloudWatch dashboards
- CloudWatch alarms
- SNS topics
- Composite alarm

**2. Đăng ký SNS Topic**

```bash
# Lấy SNS topic ARN
TOPIC_ARN=$(aws cloudformation describe-stacks \
  --stack-name EveryoneCook-dev-Observability \
  --query 'Stacks[0].Outputs[?OutputKey==`AlarmTopicArn`].OutputValue' \
  --output text)

# Đăng ký nhận email notifications
aws sns subscribe \
  --topic-arn $TOPIC_ARN \
  --protocol email \
  --notification-endpoint your-email@example.com

# Kiểm tra email và xác nhận subscription
```

![Observability đã deploy](/images/5-Workshop/5.5-deploy-infrastructure/observability-deployed.png)
*Ảnh chụp: CloudWatch console hiển thị dashboards đã tạo*

#### Bước 7: Xác minh tất cả Stacks

**1. Liệt kê tất cả Stacks**

```bash
# Liệt kê tất cả stacks đã deploy
aws cloudformation list-stacks \
  --stack-status-filter CREATE_COMPLETE \
  --query 'StackSummaries[?contains(StackName, `EveryoneCook-dev`)].StackName'
```

Phải hiển thị:
- EveryoneCook-dev-DNS
- EveryoneCook-dev-Certificate
- EveryoneCook-dev-Core
- EveryoneCook-dev-Auth
- EveryoneCook-dev-Backend
- EveryoneCook-dev-Observability

**2. Kiểm tra Stack Outputs**

```bash
# Lấy tất cả stack outputs
for stack in DNS Certificate Core Auth Backend Observability; do
  echo "=== EveryoneCook-dev-$stack ==="
  aws cloudformation describe-stacks \
    --stack-name EveryoneCook-dev-$stack \
    --query 'Stacks[0].Outputs[*].[OutputKey,OutputValue]' \
    --output table
done
```

**3. Xác minh Resources**

```bash
# DynamoDB
aws dynamodb list-tables | grep EveryoneCook

# S3
aws s3 ls | grep everyonecook

# Lambda
aws lambda list-functions | grep EveryoneCook

# API Gateway
aws apigateway get-rest-apis | grep EveryoneCook

# Cognito
aws cognito-idp list-user-pools --max-results 10 | grep EveryoneCook
```

![Tất cả Stacks đã deploy](/images/5-Workshop/5.5-deploy-infrastructure/all-stacks.png)
*Ảnh chụp: CloudFormation console hiển thị tất cả stacks đã deploy*

#### Tóm tắt Deployment

**Resources đã deploy:**
- ✅ Route 53 Hosted Zone
- ✅ 2 ACM Certificates (CloudFront + API Gateway)
- ✅ DynamoDB Table với 5 GSI indexes
- ✅ 4 S3 Buckets
- ✅ CloudFront Distribution
- ✅ 2 KMS Keys
- ✅ OpenSearch Domain (nếu bật)
- ✅ Cognito User Pool + 5 Lambda Triggers
- ✅ SES Email Identity
- ✅ API Gateway REST API
- ✅ 7 Lambda Functions (6 modules + 1 worker)
- ✅ 12 SQS Queues (6 main + 6 DLQ)
- ✅ WAF Web ACL
- ✅ 4 CloudWatch Dashboards
- ✅ CloudWatch Alarms + SNS Topics

**Tổng Resources:** 100+ AWS resources

#### Xử lý sự cố

**Vấn đề: Stack deployment thất bại**

```bash
# Kiểm tra CloudFormation events
aws cloudformation describe-stack-events \
  --stack-name EveryoneCook-dev-STACKNAME \
  --max-items 20

# Tìm events CREATE_FAILED hoặc ROLLBACK
```

**Vấn đề: Certificate validation bị kẹt**

```bash
# Kiểm tra DNS propagation
dig everyonecook.cloud

# Kiểm tra validation records trong Route 53
aws route53 list-resource-record-sets \
  --hosted-zone-id YOUR-ZONE-ID \
  --query 'ResourceRecordSets[?Type==`CNAME`]'
```

**Vấn đề: Lambda deployment thất bại**

```bash
# Đảm bảo backend code đã được chuẩn bị
cd ..
.\prepare-backend-deployment.ps1

# Kiểm tra Lambda packages tồn tại
ls services/*/deployment/
```

**Vấn đề: Không đủ quyền**

```bash
# Kiểm tra IAM permissions của bạn
aws iam get-user

# Đảm bảo bạn có AdministratorAccess hoặc tương đương
```

#### Theo dõi chi phí

**Sau Deployment:**

```bash
# Kiểm tra chi phí ước tính
aws ce get-cost-and-usage \
  --time-period Start=2024-01-01,End=2024-01-31 \
  --granularity MONTHLY \
  --metrics BlendedCost \
  --group-by Type=SERVICE
```

**Chi phí dự kiến (Dev):**
- Tháng đầu: $20-35 (không có OpenSearch)
- Với OpenSearch: $70-135
- Chi phí chính: DynamoDB, S3, CloudFront, WAF

#### Bước tiếp theo

Sau khi tất cả infrastructure đã deploy, tiến hành [Cấu hình API & Lambda](../5.6-configure-api-lambda/) để thiết lập API routes và Lambda functions.
