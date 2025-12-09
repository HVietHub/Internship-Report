---
title: "CDK Bootstrap"
date: 2025-12-01
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

#### Overview

AWS CDK Bootstrap tạo các resources cần thiết trong AWS account để deploy CDK applications. Bạn chỉ cần chạy bootstrap một lần cho mỗi account/region.

#### What is CDK Bootstrap?

CDK Bootstrap tạo:
- **S3 Bucket**: Lưu trữ CDK assets (Lambda code, Docker images)
- **ECR Repository**: Lưu trữ Docker images (nếu dùng containers)
- **IAM Roles**: Permissions cho CloudFormation và CDK
- **SSM Parameters**: Lưu trữ bootstrap version

#### Prerequisites

Đảm bảo bạn đã:
- ✅ Cài đặt AWS CLI và CDK CLI
- ✅ Configure AWS credentials
- ✅ Có quyền AdministratorAccess (hoặc tương đương)

#### Bootstrap Command

**1. Check Current Bootstrap Status**

```bash
# Check if already bootstrapped
aws cloudformation describe-stacks \
  --stack-name CDKToolkit \
  --region us-east-1

# If not bootstrapped, you'll get an error
```

**2. Run Bootstrap**

```bash
# Navigate to infrastructure directory
cd infrastructure

# Bootstrap for us-east-1 region
cdk bootstrap aws://YOUR-ACCOUNT-ID/us-east-1

# Replace YOUR-ACCOUNT-ID with your actual AWS account ID
# Get account ID: aws sts get-caller-identity --query Account --output text
```

**Example:**
```bash
# Get your account ID
ACCOUNT_ID=$(aws sts get-caller-identity --query Account --output text)

# Bootstrap
cdk bootstrap aws://$ACCOUNT_ID/us-east-1
```

![CDK Bootstrap Running](/images/5-Workshop/5.3-cdk-bootstrap/bootstrap-running.png)
*Screenshot: Terminal showing CDK bootstrap in progress*

**3. Wait for Completion**

Bootstrap takes 2-3 minutes. You'll see:
```
 ⏳  Bootstrapping environment aws://123456789012/us-east-1...
CDKToolkit: creating CloudFormation changeset...
 ✅  Environment aws://123456789012/us-east-1 bootstrapped.
```

![CDK Bootstrap Success](/images/5-Workshop/5.3-cdk-bootstrap/bootstrap-success.png)
*Screenshot: Terminal showing successful bootstrap*

#### Verify Bootstrap

**1. Check CloudFormation Stack**

```bash
# List CDK bootstrap stack
aws cloudformation describe-stacks \
  --stack-name CDKToolkit \
  --region us-east-1 \
  --query 'Stacks[0].StackStatus'

# Should return: CREATE_COMPLETE
```

**2. Check S3 Bucket**

```bash
# List CDK assets bucket
aws s3 ls | grep cdk

# Should show: cdk-hnb659fds-assets-ACCOUNT-ID-us-east-1
```

![CDK Bootstrap Resources](/images/5-Workshop/5.3-cdk-bootstrap/bootstrap-resources.png)
*Screenshot: CloudFormation console showing CDKToolkit stack*

**3. Check IAM Roles**

```bash
# List CDK roles
aws iam list-roles | grep cdk

# Should show roles like:
# - cdk-hnb659fds-cfn-exec-role-ACCOUNT-ID-us-east-1
# - cdk-hnb659fds-deploy-role-ACCOUNT-ID-us-east-1
# - cdk-hnb659fds-file-publishing-role-ACCOUNT-ID-us-east-1
```

#### Bootstrap for Multiple Regions (Optional)

If you plan to deploy to multiple regions:

```bash
# Bootstrap additional regions
cdk bootstrap aws://$ACCOUNT_ID/ap-southeast-1
cdk bootstrap aws://$ACCOUNT_ID/eu-west-1

# Note: For this workshop, we only use us-east-1
```

#### Understanding Bootstrap Resources

**S3 Bucket:**
- Stores Lambda deployment packages
- Stores CloudFormation templates
- Versioned for rollback support

**IAM Roles:**
- `cfn-exec-role`: CloudFormation execution role
- `deploy-role`: CDK deployment role
- `file-publishing-role`: Asset publishing role
- `image-publishing-role`: Docker image publishing role

**SSM Parameters:**
- `/cdk-bootstrap/hnb659fds/version`: Bootstrap version

#### Bootstrap Cost

**Free Tier:**
- S3 bucket: First 5GB free
- IAM roles: Free
- SSM parameters: Free

**Ongoing Costs:**
- S3 storage: ~$0.023/GB/month
- Typical usage: < 1GB = ~$0.02/month

#### Troubleshooting

**Issue: Access Denied**
```
Error: Need to perform AWS calls for account XXX, but no credentials found
```

Solution:
```bash
# Reconfigure AWS credentials
aws configure

# Or set environment variables
export AWS_ACCESS_KEY_ID=your-key
export AWS_SECRET_ACCESS_KEY=your-secret
export AWS_DEFAULT_REGION=us-east-1
```

**Issue: Already Bootstrapped**
```
Error: Stack [CDKToolkit] already exists
```

This is OK! Your account is already bootstrapped. You can:
- Skip this step
- Or update bootstrap: `cdk bootstrap --force`

**Issue: Insufficient Permissions**
```
Error: User is not authorized to perform: cloudformation:CreateStack
```

Solution:
- Ensure your IAM user has AdministratorAccess
- Or add specific permissions for CDK bootstrap

**Issue: Wrong Region**
```
Error: Stack is in different region
```

Solution:
```bash
# Specify region explicitly
cdk bootstrap aws://$ACCOUNT_ID/us-east-1 --region us-east-1
```

#### Bootstrap Customization (Advanced)

For production, you might want to customize bootstrap:

```bash
# Custom bootstrap with specific bucket name
cdk bootstrap \
  --toolkit-stack-name CustomCDKToolkit \
  --qualifier custom \
  aws://$ACCOUNT_ID/us-east-1
```

**Note:** For this workshop, use default bootstrap.

#### Clean Up Bootstrap (Optional)

To remove bootstrap resources (only if you're done with CDK):

```bash
# Delete CDK bootstrap stack
aws cloudformation delete-stack \
  --stack-name CDKToolkit \
  --region us-east-1

# Delete S3 bucket (must be empty first)
BUCKET_NAME=$(aws s3 ls | grep cdk | awk '{print $3}')
aws s3 rm s3://$BUCKET_NAME --recursive
aws s3 rb s3://$BUCKET_NAME
```

**Warning:** Only do this if you're completely done with CDK in this account/region!

#### Next Steps

Once bootstrap is complete, proceed to [Configure Infrastructure Stacks](../5.4-configure-stacks/) to set up your CDK stacks configuration.
