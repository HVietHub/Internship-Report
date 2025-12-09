---
title: "Cài đặt môi trường"
date: 2025-12-01
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

#### Tổng quan

Trong bước này, bạn sẽ cài đặt tất cả các công cụ cần thiết để phát triển và deploy ứng dụng EveryoneCook.

#### Các công cụ cần thiết

**1. Node.js 20.x**

```bash
# Tải từ https://nodejs.org/
# Hoặc sử dụng nvm (khuyến nghị)
nvm install 20
nvm use 20

# Kiểm tra cài đặt
node --version  # Phải là v20.x
npm --version
```

![Cài đặt Node.js](/images/5-Workshop/5.2-setup-environment/nodejs-version.png)
*Ảnh chụp: Terminal hiển thị Node.js 20.x đã được cài đặt*

**2. AWS CLI v2**

```bash
# Windows: Tải từ https://aws.amazon.com/cli/
# macOS: brew install awscli
# Linux: 
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install

# Kiểm tra cài đặt
aws --version
```

**3. AWS CDK CLI**

```bash
# Cài đặt CDK toàn cục
npm install -g aws-cdk

# Kiểm tra cài đặt
cdk --version
```

![Cài đặt CDK CLI](/images/5-Workshop/5.2-setup-environment/cdk-version.png)
*Ảnh chụp: Terminal hiển thị CDK CLI đã được cài đặt*

**4. Git**

```bash
# Tải từ https://git-scm.com/
# Hoặc sử dụng package manager

# Kiểm tra cài đặt
git --version
```

**5. Trình soạn thảo code**

Khuyến nghị: Visual Studio Code với các extension:
- AWS Toolkit
- GitLab Workflow
- ESLint
- Prettier

#### Thiết lập tài khoản AWS

**1. Tạo tài khoản AWS**

Nếu bạn chưa có tài khoản AWS:
1. Truy cập https://aws.amazon.com/
2. Nhấn "Create an AWS Account"
3. Làm theo quy trình đăng ký
4. Thêm phương thức thanh toán

**2. Tạo IAM User**

Vì lý do bảo mật, không sử dụng tài khoản root:

1. Vào IAM Console → Users → Create user
2. Tên người dùng: `everyonecook-dev`
3. Bật "Provide user access to the AWS Management Console"
4. Gắn policies:
   - AdministratorAccess (cho workshop, sử dụng quyền hạn chế hơn trong production)
5. Tạo user và lưu thông tin đăng nhập

![IAM User đã tạo](/images/5-Workshop/5.2-setup-environment/iam-user-created.png)
*Ảnh chụp: IAM console hiển thị user đã tạo với AdministratorAccess*

**3. Cấu hình AWS CLI**

```bash
# Cấu hình thông tin đăng nhập AWS
aws configure

# Nhập:
# AWS Access Key ID: [Access Key của bạn]
# AWS Secret Access Key: [Secret Key của bạn]
# Default region name: us-east-1
# Default output format: json
```

![Cấu hình AWS CLI](/images/5-Workshop/5.2-setup-environment/aws-cli-configure.png)
*Ảnh chụp: Terminal hiển thị aws configure hoàn tất*

**4. Xác minh quyền truy cập AWS**

```bash
# Kiểm tra thông tin đăng nhập AWS
aws sts get-caller-identity

# Sẽ trả về account ID và user ARN của bạn
```

#### Thiết lập Domain (Tùy chọn)

Nếu bạn muốn sử dụng domain tùy chỉnh:

**1. Đăng ký Domain**

Các lựa chọn:
- Route 53 (AWS)
- Hostinger
- GoDaddy
- Namecheap

Trong workshop này, chúng ta sử dụng: `everyonecook.cloud`

**2. Ghi nhớ Domain Registrar**

Bạn sẽ cần truy cập domain registrar để cập nhật nameservers sau này.

#### Thiết lập GitLab

**1. Tạo tài khoản GitLab**

1. Truy cập https://gitlab.com/
2. Đăng ký tài khoản miễn phí
3. Xác minh email

**2. Tạo Personal Access Token**

1. Vào GitLab → Settings → Access Tokens
2. Tên token: `everyonecook-deploy`
3. Scopes: `api`, `read_repository`, `write_repository`
4. Tạo token và lưu an toàn

![GitLab Access Token](/images/5-Workshop/5.2-setup-environment/gitlab-token.png)
*Ảnh chụp: GitLab hiển thị personal access token đã tạo*

**3. Cấu hình Git**

```bash
# Đặt tên và email của bạn
git config --global user.name "Tên của bạn"
git config --global user.email "email.cua.ban@example.com"

# Kiểm tra cấu hình
git config --list
```

#### Thiết lập Project

**1. Clone hoặc tạo Project**

Lựa chọn A: Clone project có sẵn
```bash
git clone https://gitlab.com/your-username/everyonecook.git
cd everyonecook
```

Lựa chọn B: Tạo project mới
```bash
mkdir everyonecook
cd everyonecook
git init
```

**2. Cài đặt Dependencies**

```bash
# Cài đặt tất cả dependencies
npm install

# Lệnh này cài đặt:
# - Infrastructure dependencies (CDK)
# - Backend dependencies (Lambda modules)
# - Shared dependencies
```

**3. Sao chép biến môi trường**

```bash
# Sao chép file env mẫu
cp .env.example .env

# Chỉnh sửa .env với giá trị của bạn
# Các biến quan trọng:
# - AWS_REGION=us-east-1
# - AWS_ACCOUNT_ID=your-account-id
# - DOMAIN_NAME=everyonecook.cloud
# - GITLAB_TOKEN=your-gitlab-token
```

#### Xác minh

Kiểm tra tất cả đã được cài đặt đúng:

```bash
# Kiểm tra Node.js
node --version  # v20.x

# Kiểm tra npm
npm --version   # 10.x

# Kiểm tra AWS CLI
aws --version   # aws-cli/2.x

# Kiểm tra CDK
cdk --version   # 2.x

# Kiểm tra Git
git --version   # 2.x

# Kiểm tra thông tin đăng nhập AWS
aws sts get-caller-identity

# Kiểm tra project dependencies
npm list --depth=0
```

![Xác minh môi trường](/images/5-Workshop/5.2-setup-environment/verification-complete.png)
*Ảnh chụp: Terminal hiển thị tất cả công cụ đã được xác minh*

#### Xử lý sự cố

**Vấn đề: Phiên bản Node.js không khớp**
```bash
# Sử dụng nvm để chuyển phiên bản
nvm install 20
nvm use 20
```

**Vấn đề: Không tìm thấy AWS CLI**
- Khởi động lại terminal sau khi cài đặt
- Kiểm tra biến môi trường PATH

**Vấn đề: Không tìm thấy lệnh CDK**
```bash
# Cài đặt lại CDK toàn cục
npm uninstall -g aws-cdk
npm install -g aws-cdk
```

**Vấn đề: Thông tin đăng nhập AWS không hợp lệ**
```bash
# Cấu hình lại AWS CLI
aws configure
# Nhập thông tin đăng nhập đúng
```

#### Ước tính chi phí

**Môi trường phát triển:**
- AWS Free Tier bao gồm hầu hết các dịch vụ
- Chi phí ước tính: $20-55/tháng
- Chi phí chính: DynamoDB, S3, CloudFront, Lambda

**Mẹo giảm chi phí:**
- Sử dụng pay-per-request cho DynamoDB
- Bật S3 Intelligent-Tiering
- Tắt OpenSearch trong dev (chỉ bật khi cần)
- Xóa resources khi không sử dụng

#### Bước tiếp theo

Sau khi môi trường đã được thiết lập, tiến hành [CDK Bootstrap](../5.3-cdk-bootstrap/) để chuẩn bị tài khoản AWS cho việc deploy CDK.
