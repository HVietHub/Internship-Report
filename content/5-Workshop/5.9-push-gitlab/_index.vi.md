---
title: "Push lên GitLab"
date: 2025-12-01
weight: 9
chapter: false
pre: " <b> 5.9. </b> "
---

#### Tổng quan

Sau khi test thành công, bạn sẽ push code lên GitLab để version control và chuẩn bị cho CI/CD.

#### Bước 1: Khởi tạo Git Repository

**1. Kiểm tra trạng thái Git**

```bash
# Di chuyển đến project root
cd /path/to/everyonecook-dev

# Kiểm tra xem Git đã được khởi tạo chưa
git status

# Nếu chưa khởi tạo:
git init
```

**2. Cấu hình Git**

```bash
# Đặt tên và email của bạn
git config user.name "Tên của bạn"
git config user.email "email.cua.ban@example.com"

# Xác minh cấu hình
git config --list
```

#### Bước 2: Tạo .gitignore

**1. Tạo file .gitignore**

```bash
# Tạo .gitignore
cat > .gitignore << 'EOF'
# Dependencies
node_modules/
package-lock.json
yarn.lock

# Build outputs
dist/
build/
*.js.map
*.d.ts.map
cdk.out/
.next/

# Biến môi trường
.env
.env.local
.env.*.local

# AWS
*.pem
*.key
cloudfront-private-key.pem

# Deployment packages
*.zip
deployment/

# Logs
logs/
*.log
npm-debug.log*
yarn-debug.log*
yarn-error.log*

# IDE
.vscode/
.idea/
*.swp
*.swo
*~

# OS
.DS_Store
Thumbs.db

# Test coverage
coverage/
.nyc_output/

# Temporary files
tmp/
temp/
*.tmp

# CDK
cdk.context.json
outputs.json

# TypeScript
*.tsbuildinfo
EOF
```

![Gitignore đã tạo](/images/5-Workshop/5.9-push-gitlab/gitignore.png)
*Ảnh chụp: Nội dung file .gitignore*

#### Bước 3: Stage và Commit Files

**1. Thêm Files vào Staging**

```bash
# Thêm tất cả files
git add .

# Kiểm tra những gì sẽ được commit
git status
```

**2. Tạo Initial Commit**

```bash
# Commit với message
git commit -m "Initial commit: EveryoneCook infrastructure và backend"

# Xác minh commit
git log --oneline
```

![Initial Commit](/images/5-Workshop/5.9-push-gitlab/initial-commit.png)
*Ảnh chụp: Terminal hiển thị initial commit*

#### Bước 4: Tạo GitLab Repository

**1. Đăng nhập GitLab**

Truy cập https://gitlab.com/ và đăng nhập

**2. Tạo Project mới**

1. Nhấn "New project"
2. Chọn "Create blank project"
3. Tên project: `everyonecook`
4. Visibility: Private (khuyến nghị)
5. Initialize with README: No (chúng ta đã có code)
6. Nhấn "Create project"

![GitLab Project đã tạo](/images/5-Workshop/5.9-push-gitlab/gitlab-project.png)
*Ảnh chụp: GitLab hiển thị project mới đã tạo*

**3. Lấy Repository URL**

```
# HTTPS URL
https://gitlab.com/your-username/everyonecook.git

# SSH URL (nếu bạn đã cấu hình SSH key)
git@gitlab.com:your-username/everyonecook.git
```

#### Bước 5: Thêm Remote và Push

**1. Thêm GitLab Remote**

```bash
# Thêm remote (sử dụng HTTPS)
git remote add origin https://gitlab.com/your-username/everyonecook.git

# Hoặc sử dụng SSH
git remote add origin git@gitlab.com:your-username/everyonecook.git

# Xác minh remote
git remote -v
```

**2. Push lên GitLab**

```bash
# Push lên branch main
git push -u origin main

# Nhập thông tin đăng nhập GitLab nếu sử dụng HTTPS
# Username: your-username
# Password: your-personal-access-token (không phải password!)
```

![Git Push](/images/5-Workshop/5.9-push-gitlab/git-push.png)
*Ảnh chụp: Terminal hiển thị push thành công lên GitLab*

**3. Xác minh trên GitLab**

Truy cập GitLab repository của bạn và xác minh files đã được upload.

![GitLab Repository](/images/5-Workshop/5.9-push-gitlab/gitlab-repo.png)
*Ảnh chụp: GitLab hiển thị repository với files*

#### Bước 6: Tạo cấu trúc Branch

**1. Tạo Development Branch**

```bash
# Tạo và chuyển sang dev branch
git checkout -b dev

# Push dev branch
git push -u origin dev
```

**2. Tạo Feature Branch**

```bash
# Tạo feature branch từ dev
git checkout -b feature/add-notifications

# Thực hiện thay đổi...
# Commit thay đổi...
# Push feature branch
git push -u origin feature/add-notifications
```

**3. Chiến lược Branch**

```
main (production)
  ↓
dev (development)
  ↓
feature/* (features)
hotfix/* (urgent fixes)
```

#### Bước 7: Cấu hình GitLab CI/CD

**1. Tạo .gitlab-ci.yml**

```bash
# Tạo cấu hình CI/CD
cat > .gitlab-ci.yml << 'EOF'
image: node:20

stages:
  - test
  - build
  - deploy

variables:
  AWS_DEFAULT_REGION: us-east-1

# Cache node_modules
cache:
  paths:
    - node_modules/
    - infrastructure/node_modules/
    - services/*/node_modules/

# Test stage
test:
  stage: test
  script:
    - npm install
    - npm run lint
    - npm run test
  only:
    - merge_requests
    - dev
    - main

# Build stage
build:
  stage: build
  script:
    - npm install
    - npm run build
    - cd infrastructure && npm run build
  artifacts:
    paths:
      - infrastructure/cdk.out/
      - services/*/dist/
    expire_in: 1 hour
  only:
    - dev
    - main

# Deploy lên dev
deploy:dev:
  stage: deploy
  script:
    - npm install -g aws-cdk
    - cd infrastructure
    - npx cdk deploy --all --require-approval never --context environment=dev
  environment:
    name: development
    url: https://api.everyonecook.cloud
  only:
    - dev
  when: manual

# Deploy lên production
deploy:prod:
  stage: deploy
  script:
    - npm install -g aws-cdk
    - cd infrastructure
    - npx cdk deploy --all --require-approval never --context environment=prod
  environment:
    name: production
    url: https://api.everyonecook.cloud
  only:
    - main
  when: manual
EOF
```

**2. Commit cấu hình CI/CD**

```bash
# Thêm và commit
git add .gitlab-ci.yml
git commit -m "Thêm cấu hình GitLab CI/CD"
git push
```

![GitLab CI/CD](/images/5-Workshop/5.9-push-gitlab/gitlab-cicd.png)
*Ảnh chụp: GitLab hiển thị CI/CD pipeline*

#### Bước 8: Cấu hình GitLab Variables

**1. Thêm AWS Credentials**

1. Vào GitLab → Settings → CI/CD → Variables
2. Thêm variables:
   - `AWS_ACCESS_KEY_ID`: AWS access key của bạn
   - `AWS_SECRET_ACCESS_KEY`: AWS secret key của bạn (masked)
   - `AWS_DEFAULT_REGION`: us-east-1

![GitLab Variables](/images/5-Workshop/5.9-push-gitlab/gitlab-variables.png)
*Ảnh chụp: Cấu hình GitLab CI/CD variables*

**2. Thêm biến môi trường khác**

Thêm các biến môi trường khác:
- `DOMAIN_NAME`: everyonecook.cloud
- `ENABLE_OPENSEARCH`: false
- etc.

#### Bước 9: Thiết lập Branch Protection

**1. Bảo vệ Main Branch**

1. Vào Settings → Repository → Protected branches
2. Chọn branch `main`
3. Allowed to merge: Maintainers
4. Allowed to push: No one
5. Require approval: Yes (1 approval)

**2. Bảo vệ Dev Branch**

1. Chọn branch `dev`
2. Allowed to merge: Developers + Maintainers
3. Allowed to push: Developers + Maintainers

#### Bước 10: Tạo Merge Request Workflow

**1. Tạo Feature Branch**

```bash
# Tạo feature branch
git checkout dev
git pull
git checkout -b feature/new-feature

# Thực hiện thay đổi
# ...

# Commit thay đổi
git add .
git commit -m "Thêm tính năng mới"
git push -u origin feature/new-feature
```

**2. Tạo Merge Request**

1. Vào GitLab → Merge requests → New merge request
2. Source branch: feature/new-feature
3. Target branch: dev
4. Title: "Thêm tính năng mới"
5. Description: Mô tả thay đổi
6. Assign to reviewer
7. Create merge request

**3. Review và Merge**

1. Reviewer review code
2. CI/CD pipeline chạy tests
3. Nếu tests pass và được approve, merge vào dev
4. Xóa feature branch

#### Bước 11: Tag Releases

**1. Tạo Release Tag**

```bash
# Sau khi merge vào main
git checkout main
git pull

# Tạo tag
git tag -a v1.0.0 -m "Release version 1.0.0"

# Push tag
git push origin v1.0.0
```

**2. Tạo Release trên GitLab**

1. Vào Repository → Tags
2. Tìm tag của bạn
3. Nhấn "Create release"
4. Thêm release notes
5. Publish release

#### Tóm tắt Git Workflow

```
1. Tạo feature branch từ dev
   git checkout -b feature/new-feature

2. Thực hiện thay đổi và commit
   git add .
   git commit -m "Mô tả"

3. Push lên GitLab
   git push -u origin feature/new-feature

4. Tạo merge request trên GitLab
   feature/new-feature → dev

5. Review, test, và merge

6. Deploy lên môi trường dev
   (trigger thủ công trong GitLab CI/CD)

7. Test trong môi trường dev

8. Merge dev → main cho production

9. Deploy lên production
   (trigger thủ công trong GitLab CI/CD)

10. Tag release
    git tag -a v1.0.0 -m "Release"
```

#### Best Practices

**Commit Messages:**
```
feat: Thêm xác thực người dùng
fix: Sửa lỗi đăng nhập
docs: Cập nhật README
style: Format code
refactor: Refactor user service
test: Thêm unit tests
chore: Cập nhật dependencies
```

**Đặt tên Branch:**
```
feature/add-notifications
bugfix/fix-login-error
hotfix/critical-security-patch
release/v1.0.0
```

#### Xử lý sự cố

**Vấn đề: Xác thực thất bại**

```bash
# Sử dụng personal access token thay vì password
# Tạo token: GitLab → Settings → Access Tokens
# Sử dụng token làm password khi push
```

**Vấn đề: Files lớn bị từ chối**

```bash
# Kiểm tra kích thước file
git ls-files -z | xargs -0 du -h | sort -h

# Xóa files lớn khỏi history
git filter-branch --tree-filter 'rm -f large-file.zip' HEAD
```

**Vấn đề: Merge conflicts**

```bash
# Cập nhật branch của bạn
git checkout feature/your-feature
git fetch origin
git merge origin/dev

# Giải quyết conflicts trong files
# Sau đó commit
git add .
git commit -m "Giải quyết merge conflicts"
git push
```

#### Bước tiếp theo

Sau khi code đã push lên GitLab, tiến hành [Deploy lên Amplify](../5.10-deploy-amplify/) để deploy ứng dụng frontend của bạn.
