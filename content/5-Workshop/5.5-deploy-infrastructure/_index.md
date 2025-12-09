---
title: "Deploy Infrastructure"
date: 2025-12-01
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

#### Overview

Trong bước này, bạn sẽ deploy tất cả infrastructure stacks lên AWS theo đúng thứ tự dependencies. Quá trình này mất khoảng 30-45 phút.

#### Deployment Order

```
1. DNS Stack          (2-3 phút)
2. Certificate Stack  (5-10 phút - DNS validation)
3. Core Stack         (10-15 phút - CloudFront + OpenSearch)
4. Auth Stack         (5-7 phút - Cognito + SES)
5. Backend Stack      (8-12 phút - API Gateway + Lambda)
6. Observability Stack (3-5 phút - CloudWatch)
```

**Total time: 30-50 phút**

#### Step 1: Deploy DNS Stack

**1. Deploy Stack**

```bash
# Navigate to infrastructure directory
cd infrastructure

# Deploy DNS stack
npx cdk deploy EveryoneCook-dev-DNS --context environment=dev

# Review changes and type 'y' to confirm
```

![DNS Stack Deploy](/images/5-Workshop/5.5-deploy-infrastructure/dns-deploy.png)
*Screenshot: Terminal showing DNS stack deployment*

**2. Get Nameservers**

Sau khi deploy xong, lưu lại 4 nameservers từ output:

```
Outputs:
EveryoneCook-dev-DNS.NameServers = ns-123.awsdns-12.com, ns-456.awsdns-45.net, ...
EveryoneCook-dev-DNS.HostedZoneId = Z1234567890ABC
```

**3. Update Domain Registrar**

Đi đến domain registrar (Hostinger, GoDaddy, etc.) và update nameservers:

1. Login vào domain registrar
2. Tìm domain của bạn (everyonecook.cloud)
3. Chọn "Custom Nameservers"
4. Nhập 4 nameservers từ AWS
5. Save changes

![Hostinger Nameservers](/images/5-Workshop/5.5-deploy-infrastructure/hostinger-nameservers.png)
*Screenshot: Hostinger showing nameservers updated*

**4. Wait for DNS Propagation**

```bash
# Check DNS propagation (có thể mất 5-30 phút)
dig NS everyonecook.cloud

# Hoặc dùng online tool: https://www.whatsmydns.net/
```

#### Step 2: Deploy Certificate Stack

**Important:** Stack này phải deploy ở us-east-1 region (CloudFront requirement)

**1. Deploy Stack**

```bash
# Deploy Certificate stack
npx cdk deploy EveryoneCook-dev-Certificate --context environment=dev

# Type 'y' to confirm
```

**2. Wait for DNS Validation**

ACM sẽ tự động:
- Tạo CNAME records trong Route 53
- Validate domain ownership
- Issue certificates

Quá trình này mất 5-10 phút.

![Certificate Validation](/images/5-Workshop/5.5-deploy-infrastructure/certificate-validation.png)
*Screenshot: ACM showing certificates being validated*

**3. Verify Certificates**

```bash
# Check certificate status
aws acm list-certificates --region us-east-1

# Both certificates should show Status: ISSUED
```

![Certificates Issued](/images/5-Workshop/5.5-deploy-infrastructure/certificates-issued.png)
*Screenshot: ACM console showing both certificates issued*

#### Step 3: Deploy Core Stack

**1. Deploy Stack**

```bash
# Deploy Core stack (takes 10-15 minutes)
npx cdk deploy EveryoneCook-dev-Core --context environment=dev

# Type 'y' to confirm
```

Deployment includes:
- DynamoDB table creation
- 4 S3 buckets
- CloudFront distribution (takes longest)
- 2 KMS keys
- OpenSearch domain (if enabled)

![Core Stack Deploy](/images/5-Workshop/5.5-deploy-infrastructure/core-deploy.png)
*Screenshot: Terminal showing Core stack deployment progress*

**2. Monitor Deployment**

```bash
# In another terminal, watch CloudFormation events
aws cloudformation describe-stack-events \
  --stack-name EveryoneCook-dev-Core \
  --max-items 10 \
  --query 'StackEvents[*].[Timestamp,ResourceStatus,ResourceType,LogicalResourceId]' \
  --output table
```

**3. Verify Resources**

```bash
# Check DynamoDB table
aws dynamodb describe-table --table-name EveryoneCook-dev

# Check S3 buckets
aws s3 ls | grep everyonecook

# Check CloudFront distribution
aws cloudfront list-distributions --query 'DistributionList.Items[*].[Id,DomainName,Status]'
```

![Core Resources](/images/5-Workshop/5.5-deploy-infrastructure/core-resources.png)
*Screenshot: AWS Console showing Core stack resources*

#### Step 4: Deploy Auth Stack

**1. Deploy Stack**

```bash
# Deploy Auth stack
npx cdk deploy EveryoneCook-dev-Auth --context environment=dev

# Type 'y' to confirm
```

Deployment includes:
- Cognito User Pool
- 5 Lambda triggers
- SES email identity
- IAM roles

**2. Verify SES Email Identity**

```bash
# Check SES identity status
aws sesv2 get-email-identity --email-identity everyonecook.cloud

# Should show: VerificationStatus: SUCCESS
```

**3. Request SES Production Access**

Nếu muốn gửi email thực:

1. Go to SES Console → Account dashboard
2. Click "Request production access"
3. Fill form:
   - Use case: Transactional emails
   - Website: https://everyonecook.cloud
   - Describe bounce handling
4. Submit (usually approved in 24 hours)

![SES Verified](/images/5-Workshop/5.5-deploy-infrastructure/ses-verified.png)
*Screenshot: SES console showing email identity verified*

#### Step 5: Deploy Backend Stack

**1. Prepare Backend Code**

```bash
# Navigate to project root
cd ..

# Build and prepare all Lambda modules
.\prepare-backend-deployment.ps1

# This script:
# - Compiles TypeScript to JavaScript
# - Installs production dependencies
# - Creates deployment packages
```

![Backend Preparation](/images/5-Workshop/5.5-deploy-infrastructure/backend-prep.png)
*Screenshot: Terminal showing backend preparation*

**2. Deploy Stack**

```bash
# Navigate back to infrastructure
cd infrastructure

# Deploy Backend stack
npx cdk deploy EveryoneCook-dev-Backend --context environment=dev

# Type 'y' to confirm
```

Deployment includes:
- API Gateway REST API
- 6 Lambda modules + 1 worker
- 6 SQS queues + 6 DLQs
- WAF Web ACL

**3. Verify API Gateway**

```bash
# Get API endpoint
aws cloudformation describe-stacks \
  --stack-name EveryoneCook-dev-Backend \
  --query 'Stacks[0].Outputs[?OutputKey==`ApiEndpoint`].OutputValue' \
  --output text

# Test health endpoint
curl https://api.everyonecook.cloud/health
```

![Backend Deployed](/images/5-Workshop/5.5-deploy-infrastructure/backend-deployed.png)
*Screenshot: API Gateway console showing REST API deployed*

#### Step 6: Deploy Observability Stack

**1. Deploy Stack**

```bash
# Deploy Observability stack
npx cdk deploy EveryoneCook-dev-Observability --context environment=dev

# Type 'y' to confirm
```

Deployment includes:
- 4 CloudWatch dashboards
- CloudWatch alarms
- SNS topics
- Composite alarm

**2. Subscribe to SNS Topic**

```bash
# Get SNS topic ARN
TOPIC_ARN=$(aws cloudformation describe-stacks \
  --stack-name EveryoneCook-dev-Observability \
  --query 'Stacks[0].Outputs[?OutputKey==`AlarmTopicArn`].OutputValue' \
  --output text)

# Subscribe to email notifications
aws sns subscribe \
  --topic-arn $TOPIC_ARN \
  --protocol email \
  --notification-endpoint your-email@example.com

# Check email and confirm subscription
```

![Observability Deployed](/images/5-Workshop/5.5-deploy-infrastructure/observability-deployed.png)
*Screenshot: CloudWatch console showing dashboards created*

#### Step 7: Verify All Stacks

**1. List All Stacks**

```bash
# List all deployed stacks
aws cloudformation list-stacks \
  --stack-status-filter CREATE_COMPLETE \
  --query 'StackSummaries[?contains(StackName, `EveryoneCook-dev`)].StackName'
```

Should show:
- EveryoneCook-dev-DNS
- EveryoneCook-dev-Certificate
- EveryoneCook-dev-Core
- EveryoneCook-dev-Auth
- EveryoneCook-dev-Backend
- EveryoneCook-dev-Observability

**2. Check Stack Outputs**

```bash
# Get all stack outputs
for stack in DNS Certificate Core Auth Backend Observability; do
  echo "=== EveryoneCook-dev-$stack ==="
  aws cloudformation describe-stacks \
    --stack-name EveryoneCook-dev-$stack \
    --query 'Stacks[0].Outputs[*].[OutputKey,OutputValue]' \
    --output table
done
```

**3. Verify Resources**

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

![All Stacks Deployed](/images/5-Workshop/5.5-deploy-infrastructure/all-stacks.png)
*Screenshot: CloudFormation console showing all stacks deployed*

#### Deployment Summary

**Deployed Resources:**
- ✅ Route 53 Hosted Zone
- ✅ 2 ACM Certificates (CloudFront + API Gateway)
- ✅ DynamoDB Table with 5 GSI indexes
- ✅ 4 S3 Buckets
- ✅ CloudFront Distribution
- ✅ 2 KMS Keys
- ✅ OpenSearch Domain (if enabled)
- ✅ Cognito User Pool + 5 Lambda Triggers
- ✅ SES Email Identity
- ✅ API Gateway REST API
- ✅ 7 Lambda Functions (6 modules + 1 worker)
- ✅ 12 SQS Queues (6 main + 6 DLQ)
- ✅ WAF Web ACL
- ✅ 4 CloudWatch Dashboards
- ✅ CloudWatch Alarms + SNS Topics

**Total Resources:** ~100+ AWS resources

#### Troubleshooting

**Issue: Stack deployment fails**

```bash
# Check CloudFormation events
aws cloudformation describe-stack-events \
  --stack-name EveryoneCook-dev-STACKNAME \
  --max-items 20

# Look for CREATE_FAILED or ROLLBACK events
```

**Issue: Certificate validation stuck**

```bash
# Check DNS propagation
dig everyonecook.cloud

# Check validation records in Route 53
aws route53 list-resource-record-sets \
  --hosted-zone-id YOUR-ZONE-ID \
  --query 'ResourceRecordSets[?Type==`CNAME`]'
```

**Issue: Lambda deployment fails**

```bash
# Ensure backend code is prepared
cd ..
.\prepare-backend-deployment.ps1

# Check Lambda packages exist
ls services/*/deployment/
```

**Issue: Insufficient permissions**

```bash
# Check your IAM permissions
aws iam get-user

# Ensure you have AdministratorAccess or equivalent
```

#### Cost Tracking

**After Deployment:**

```bash
# Check estimated costs
aws ce get-cost-and-usage \
  --time-period Start=2024-01-01,End=2024-01-31 \
  --granularity MONTHLY \
  --metrics BlendedCost \
  --group-by Type=SERVICE
```

**Expected Costs (Dev):**
- First month: $20-35 (without OpenSearch)
- With OpenSearch: $70-135
- Most costs: DynamoDB, S3, CloudFront, WAF

#### Next Steps

Once all infrastructure is deployed, proceed to [Configure API & Lambda](../5.6-configure-api-lambda/) to set up your API routes and Lambda functions.
