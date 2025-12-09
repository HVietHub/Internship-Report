---
title: "Deploy lên Amplify"
date: 2025-12-01
weight: 10
chapter: false
pre: " <b> 5.10. </b> "
---

#### Tổng quan

Bước cuối cùng là deploy frontend Next.js 15 lên AWS Amplify với tích hợp GitLab để tự động deploy khi có code mới.

#### Bước 1: Chuẩn bị Frontend Code

**1. Kiểm tra cấu trúc Frontend**

```bash
cd frontend

# Kiểm tra package.json
cat package.json

# Phải hiển thị Next.js 15
```

**2. Test Frontend cục bộ**

```bash
# Cài đặt dependencies
npm install

# Chạy development server
npm run dev

# Mở http://localhost:3000
```

**3. Build Frontend**

```bash
# Build cho production
npm run build

# Test production build
npm start
```

#### Bước 2: Tạo Amplify App

**1. Vào AWS Amplify Console**

1. Mở AWS Console
2. Tìm kiếm "Amplify"
3. Nhấn "Get started" hoặc "New app"

**2. Kết nối với GitLab**

1. Chọn "Host web app"
2. Chọn "GitLab"
3. Nhấn "Connect branch"
4. Cho phép AWS Amplify truy cập GitLab

![Kết nối Amplify GitLab](/images/5-Workshop/5.10-deploy-amplify/amplify-gitlab-connect.png)
*Ảnh chụp: Amplify hiển thị kết nối GitLab*

**3. Chọn Repository**

1. Chọn repository: `everyonecook`
2. Chọn branch: `main` (hoặc `dev` cho development)
3. Nhấn "Next"

#### Bước 3: Cấu hình Build Settings

**1. Tên App**

```
App name: everyonecook-frontend
```

**2. Build Settings**

Amplify tự động phát hiện Next.js, nhưng hãy xác minh:

```yaml
version: 1
frontend:
  phases:
    preBuild:
      commands:
        - npm ci
    build:
      commands:
        - npm run build
  artifacts:
    baseDirectory: .next
    files:
      - '**/*'
  cache:
    paths:
      - node_modules/**/*
      - .next/cache/**/*
```

![Amplify Build Settings](/images/5-Workshop/5.10-deploy-amplify/amplify-build-settings.png)
*Ảnh chụp: Amplify build settings cho Next.js*

**3. Advanced Settings**

Thêm biến môi trường:
```
NEXT_PUBLIC_API_URL=https://api.everyonecook.cloud
NEXT_PUBLIC_CDN_URL=https://cdn.everyonecook.cloud
NEXT_PUBLIC_USER_POOL_ID=us-east-1_ABC123
NEXT_PUBLIC_USER_POOL_CLIENT_ID=abc123def456
NEXT_PUBLIC_REGION=us-east-1
```

![Amplify Environment Variables](/images/5-Workshop/5.10-deploy-amplify/amplify-env-vars.png)
*Ảnh chụp: Cấu hình biến môi trường Amplify*

#### Bước 4: Deploy Frontend

**1. Bắt đầu Deployment**

Nhấn "Save and deploy"

Amplify sẽ:
1. Clone repository từ GitLab
2. Cài đặt dependencies
3. Build Next.js app
4. Deploy lên CDN
5. Cung cấp domain

**2. Theo dõi Deployment**

Theo dõi tiến trình deployment:
- Provision
- Build
- Deploy
- Verify

![Amplify Deployment](/images/5-Workshop/5.10-deploy-amplify/amplify-deployment.png)
*Ảnh chụp: Amplify hiển thị deployment đang tiến hành*

**3. Chờ hoàn tất**

Deployment mất 5-10 phút.

![Amplify Deployment thành công](/images/5-Workshop/5.10-deploy-amplify/amplify-success.png)
*Ảnh chụp: Amplify hiển thị deployment thành công*

#### Bước 5: Cấu hình Custom Domain

**1. Thêm Custom Domain**

1. Vào Amplify app → Domain management
2. Nhấn "Add domain"
3. Nhập domain: `everyonecook.cloud`
4. Amplify sẽ tự động cấu hình:
   - Root domain: `everyonecook.cloud`
   - WWW subdomain: `www.everyonecook.cloud`

**2. Cấu hình DNS**

Amplify tự động tạo DNS records trong Route 53:
```
everyonecook.cloud → A record → Amplify
www.everyonecook.cloud → CNAME → Amplify
```

**3. SSL Certificate**

Amplify tự động cung cấp SSL certificate qua ACM.

**4. Chờ DNS Propagation**

Mất 5-15 phút.

![Amplify Custom Domain](/images/5-Workshop/5.10-deploy-amplify/amplify-custom-domain.png)
*Ảnh chụp: Amplify hiển thị custom domain đã cấu hình*

#### Bước 6: Xác minh Deployment

**1. Truy cập Frontend**

```bash
# Qua Amplify domain
curl -I https://main.d1234567890.amplifyapp.com

# Qua custom domain
curl -I https://everyonecook.cloud

# Phải trả về 200 OK
```

**2. Test các tính năng Frontend**

1. Mở https://everyonecook.cloud trong trình duyệt
2. Test đăng ký người dùng
3. Test đăng nhập
4. Test tạo posts
5. Test tạo recipes
6. Test tính năng AI

![Frontend Live](/images/5-Workshop/5.10-deploy-amplify/frontend-live.png)
*Ảnh chụp: Trình duyệt hiển thị EveryoneCook frontend live*

#### Bước 7: Cấu hình Auto-Deploy

**1. Bật Auto-Deploy**

Auto-deploy được bật mặc định. Khi bạn push lên GitLab:
```bash
git push origin main
```

Amplify tự động:
1. Phát hiện commit mới
2. Bắt đầu build
3. Deploy phiên bản mới
4. Cập nhật live site

**2. Cấu hình Branch Deployments**

Thiết lập nhiều môi trường:

1. Main branch → Production (everyonecook.cloud)
2. Dev branch → Development (dev.everyonecook.cloud)

Thêm dev branch:
1. Vào Amplify → App settings → General
2. Nhấn "Connect branch"
3. Chọn branch `dev`
4. Cấu hình subdomain: `dev.everyonecook.cloud`

![Amplify Branch Deployments](/images/5-Workshop/5.10-deploy-amplify/amplify-branches.png)
*Ảnh chụp: Amplify hiển thị nhiều branch deployments*

#### Bước 8: Cấu hình Build Notifications

**1. Thêm SNS Notifications**

1. Vào Amplify → Notifications
2. Nhấn "Add notification"
3. Chọn events:
   - Build started
   - Build succeeded
   - Build failed
4. Nhập địa chỉ email
5. Lưu

**2. Tích hợp Slack (Tùy chọn)**

1. Tạo Slack webhook
2. Vào Amplify → Notifications
3. Thêm webhook URL
4. Test notification

#### Bước 9: Theo dõi hiệu suất

**1. Kiểm tra Amplify Metrics**

1. Vào Amplify → Monitoring
2. Xem metrics:
   - Requests
   - Data transfer
   - Errors
   - Latency

**2. Bật CloudWatch Logs**

```bash
# Xem Amplify build logs
aws amplify get-job \
  --app-id d1234567890 \
  --branch-name main \
  --job-id 1
```

#### Bước 10: Cấu hình Redirects và Rewrites

**1. Thêm Redirects**

Tạo `amplify.yml` trong frontend root:

```yaml
version: 1
frontend:
  phases:
    preBuild:
      commands:
        - npm ci
    build:
      commands:
        - npm run build
  artifacts:
    baseDirectory: .next
    files:
      - '**/*'
  cache:
    paths:
      - node_modules/**/*
      - .next/cache/**/*
customHeaders:
  - pattern: '**/*'
    headers:
      - key: 'Strict-Transport-Security'
        value: 'max-age=31536000; includeSubDomains'
      - key: 'X-Frame-Options'
        value: 'SAMEORIGIN'
      - key: 'X-Content-Type-Options'
        value: 'nosniff'
redirects:
  - source: '/<*>'
    target: '/index.html'
    status: '404-200'
```

**2. Commit và Push**

```bash
git add amplify.yml
git commit -m "Thêm cấu hình Amplify"
git push origin main
```

#### Tóm tắt Deployment

**Resources đã deploy:**
- ✅ Amplify App
- ✅ Frontend hosted trên Amplify CDN
- ✅ Custom domain đã cấu hình
- ✅ SSL certificate đã cung cấp
- ✅ Auto-deploy từ GitLab đã bật
- ✅ Nhiều branch deployments
- ✅ Build notifications đã cấu hình

**URLs:**
- Production: https://everyonecook.cloud
- Development: https://dev.everyonecook.cloud (nếu cấu hình)
- Amplify: https://main.d1234567890.amplifyapp.com

#### Chi phí

**Amplify Hosting:**
- Build minutes: 1,000 phút đầu/tháng miễn phí
- Hosting: 15 GB đầu tiên served/tháng miễn phí
- Sau free tier: $0.01 mỗi build minute, $0.15 mỗi GB served

**Chi phí ước tính:**
- Môi trường Dev: $0-5/tháng
- Production (traffic thấp): $5-20/tháng
- Production (traffic cao): $20-100/tháng

#### Xử lý sự cố

**Vấn đề: Build thất bại**

```bash
# Kiểm tra build logs trong Amplify console
# Các vấn đề thường gặp:
# - Thiếu biến môi trường
# - Phiên bản Node không khớp
# - Build command không đúng
```

**Vấn đề: Custom domain không hoạt động**

```bash
# Kiểm tra DNS propagation
dig everyonecook.cloud

# Kiểm tra Route 53 records
aws route53 list-resource-record-sets \
  --hosted-zone-id YOUR-ZONE-ID
```

**Vấn đề: Biến môi trường không hoạt động**

```bash
# Đảm bảo variables bắt đầu với NEXT_PUBLIC_ cho client-side
# Redeploy sau khi thêm variables
```

#### Best Practices

1. **Sử dụng biến môi trường**: Không hardcode API URLs
2. **Bật Auto-Deploy**: Tự động deployments khi push
3. **Nhiều môi trường**: Tách biệt dev và prod
4. **Theo dõi hiệu suất**: Sử dụng Amplify metrics
5. **Thiết lập Notifications**: Nhận thông báo khi build thất bại
6. **Sử dụng Custom Domain**: Giao diện chuyên nghiệp
7. **Bật HTTPS**: Luôn sử dụng SSL
8. **Cấu hình Headers**: Security headers để bảo vệ

#### Bước tiếp theo

Chúc mừng! Ứng dụng của bạn đã được deploy hoàn toàn:
- ✅ Infrastructure trên AWS
- ✅ Backend APIs đang chạy
- ✅ Frontend trên Amplify
- ✅ Code trên GitLab
- ✅ CI/CD đã cấu hình

Tiến hành [Dọn dẹp](../5.11-cleanup/) khi bạn hoàn thành testing, hoặc bắt đầu sử dụng ứng dụng của bạn!
