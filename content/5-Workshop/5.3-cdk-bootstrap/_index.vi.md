---
title: "CDK Bootstrap"
date: 2025-12-01
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

#### Tổng quan

AWS CDK Bootstrap tạo các resources cần thiết trong AWS account để deploy CDK applications. Bạn chỉ cần chạy bootstrap một lần cho mỗi account/region.

#### CDK Bootstrap là gì?

CDK Bootstrap tạo:
- **S3 Bucket**: Lưu trữ CDK assets (Lambda code, Docker images)
- **ECR Repository**: Lưu trữ Docker images (nếu dùng containers)
- **IAM Roles**: Permissions cho CloudFormation và CDK
- **SSM Parameters**: Lưu trữ bootstrap version

#### Điều kiện tiên quyết

Đảm bảo bạn đã:
- ✅ Cài đặt AWS CLI và CDK CLI
- ✅ Cấu hình AWS credentials
- ✅ Có quyền AdministratorAccess (hoặc tương đương)

#### Lệnh Bootstrap

**1. Kiểm tra trạng thái Bootstrap hiện tại**

```bash
# Kiểm tra xem đã bootstrap chưa
aws cloudformation describe-stacks \
  --stack-name CDKToolkit \
  --region us-east-1

# Nếu chưa bootstrap, bạn sẽ nhận được lỗi
```

**2. Chạy Bootstrap**

```bash
# Di chuyển đến thư mục infrastructure
cd infrastructure

# Bootstrap cho region us-east-1
cdk bootstrap aws://YOUR-ACCOUNT-ID/us-east-1

# Thay YOUR-ACCOUNT-ID bằng AWS account ID thực của bạn
# Lấy account ID: aws sts get-caller-identity --query Account --output text
```

**Ví dụ:**
```bash
# Lấy account ID của bạn
ACCOUNT_ID=$(aws sts get-caller-identity --query Account --output text)

# Bootstrap
cdk bootstrap aws://$ACCOUNT_ID/us-east-1
```

![CDK Bootstrap đang chạy](/images/5-Workshop/5.3-cdk-bootstrap/bootstrap-running.png)
*Ảnh chụp: Terminal hiển thị CDK bootstrap đang tiến hành*

**3. Chờ hoàn tất**

Bootstrap mất 2-3 phút. Bạn sẽ thấy:
```
 ⏳  Bootstrapping environment aws://123456789012/us-east-1...
CDKToolkit: creating CloudFormation changeset...
 ✅  Environment aws://123456789012/us-east-1 bootstrapped.
```

![CDK Bootstrap thành công](/images/5-Workshop/5.3-cdk-bootstrap/bootstrap-success.png)
*Ảnh chụp: Terminal hiển thị bootstrap thành công*

#### Xác minh Bootstrap

**1. Kiểm tra CloudFormation Stack**

```bash
# Liệt kê CDK bootstrap stack
aws cloudformation describe-stacks \
  --stack-name CDKToolkit \
  --region us-east-1 \
  --query 'Stacks[0].StackStatus'

# Phải trả về: CREATE_COMPLETE
```

**2. Kiểm tra S3 Bucket**

```bash
# Liệt kê CDK assets bucket
aws s3 ls | grep cdk

# Phải hiển thị: cdk-hnb659fds-assets-ACCOUNT-ID-us-east-1
```

![CDK Bootstrap Resources](/images/5-Workshop/5.3-cdk-bootstrap/bootstrap-resources.png)
*Ảnh chụp: CloudFormation console hiển thị CDKToolkit stack*

**3. Kiểm tra IAM Roles**

```bash
# Liệt kê CDK roles
aws iam list-roles | grep cdk

# Phải hiển thị các roles như:
# - cdk-hnb659fds-cfn-exec-role-ACCOUNT-ID-us-east-1
# - cdk-hnb659fds-deploy-role-ACCOUNT-ID-us-east-1
# - cdk-hnb659fds-file-publishing-role-ACCOUNT-ID-us-east-1
```

#### Bootstrap cho nhiều Regions (Tùy chọn)

Nếu bạn dự định deploy đến nhiều regions:

```bash
# Bootstrap các regions bổ sung
cdk bootstrap aws://$ACCOUNT_ID/ap-southeast-1
cdk bootstrap aws://$ACCOUNT_ID/eu-west-1

# Lưu ý: Trong workshop này, chúng ta chỉ sử dụng us-east-1
```

#### Hiểu về Bootstrap Resources

**S3 Bucket:**
- Lưu trữ Lambda deployment packages
- Lưu trữ CloudFormation templates
- Có versioning để hỗ trợ rollback

**IAM Roles:**
- `cfn-exec-role`: CloudFormation execution role
- `deploy-role`: CDK deployment role
- `file-publishing-role`: Asset publishing role
- `image-publishing-role`: Docker image publishing role

**SSM Parameters:**
- `/cdk-bootstrap/hnb659fds/version`: Bootstrap version

#### Chi phí Bootstrap

**Free Tier:**
- S3 bucket: 5GB đầu tiên miễn phí
- IAM roles: Miễn phí
- SSM parameters: Miễn phí

**Chi phí liên tục:**
- S3 storage: ~$0.023/GB/tháng
- Sử dụng thông thường: < 1GB = ~$0.02/tháng

#### Xử lý sự cố

**Vấn đề: Access Denied**
```
Error: Need to perform AWS calls for account XXX, but no credentials found
```

Giải pháp:
```bash
# Cấu hình lại AWS credentials
aws configure

# Hoặc đặt biến môi trường
export AWS_ACCESS_KEY_ID=your-key
export AWS_SECRET_ACCESS_KEY=your-secret
export AWS_DEFAULT_REGION=us-east-1
```

**Vấn đề: Đã Bootstrap rồi**
```
Error: Stack [CDKToolkit] already exists
```

Điều này OK! Account của bạn đã được bootstrap. Bạn có thể:
- Bỏ qua bước này
- Hoặc cập nhật bootstrap: `cdk bootstrap --force`

**Vấn đề: Không đủ quyền**
```
Error: User is not authorized to perform: cloudformation:CreateStack
```

Giải pháp:
- Đảm bảo IAM user của bạn có AdministratorAccess
- Hoặc thêm permissions cụ thể cho CDK bootstrap

**Vấn đề: Sai Region**
```
Error: Stack is in different region
```

Giải pháp:
```bash
# Chỉ định region rõ ràng
cdk bootstrap aws://$ACCOUNT_ID/us-east-1 --region us-east-1
```

#### Tùy chỉnh Bootstrap (Nâng cao)

Cho production, bạn có thể muốn tùy chỉnh bootstrap:

```bash
# Bootstrap tùy chỉnh với tên bucket cụ thể
cdk bootstrap \
  --toolkit-stack-name CustomCDKToolkit \
  --qualifier custom \
  aws://$ACCOUNT_ID/us-east-1
```

**Lưu ý:** Trong workshop này, sử dụng bootstrap mặc định.

#### Dọn dẹp Bootstrap (Tùy chọn)

Để xóa bootstrap resources (chỉ khi bạn hoàn toàn xong với CDK):

```bash
# Xóa CDK bootstrap stack
aws cloudformation delete-stack \
  --stack-name CDKToolkit \
  --region us-east-1

# Xóa S3 bucket (phải empty trước)
BUCKET_NAME=$(aws s3 ls | grep cdk | awk '{print $3}')
aws s3 rm s3://$BUCKET_NAME --recursive
aws s3 rb s3://$BUCKET_NAME
```

**Cảnh báo:** Chỉ làm điều này nếu bạn hoàn toàn xong với CDK trong account/region này!

#### Bước tiếp theo

Sau khi bootstrap hoàn tất, tiến hành [Cấu hình Infrastructure Stacks](../5.4-configure-stacks/) để thiết lập cấu hình CDK stacks của bạn.
