---
title: "Push to GitLab"
date: 2025-12-01
weight: 9
chapter: false
pre: " <b> 5.9. </b> "
---

#### Overview

Sau khi test thành công, bạn sẽ push code lên GitLab để version control và chuẩn bị cho CI/CD.

#### Step 1: Initialize Git Repository

**1. Check Git Status**

```bash
# Navigate to project root
cd /path/to/everyonecook-dev

# Check if Git is already initialized
git status

# If not initialized:
git init
```

**2. Configure Git**

```bash
# Set your name and email
git config user.name "Your Name"
git config user.email "your.email@example.com"

# Verify configuration
git config --list
```

#### Step 2: Create .gitignore

**1. Create .gitignore File**

```bash
# Create .gitignore
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

# Environment variables
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

![Gitignore Created](/images/5-Workshop/5.9-push-gitlab/gitignore.png)
*Screenshot: .gitignore file content*

#### Step 3: Stage and Commit Files

**1. Add Files to Staging**

```bash
# Add all files
git add .

# Check what will be committed
git status
```

**2. Create Initial Commit**

```bash
# Commit with message
git commit -m "Initial commit: EveryoneCook infrastructure and backend"

# Verify commit
git log --oneline
```

![Initial Commit](/images/5-Workshop/5.9-push-gitlab/initial-commit.png)
*Screenshot: Terminal showing initial commit*

#### Step 4: Create GitLab Repository

**1. Login to GitLab**

Go to https://gitlab.com/ and login

**2. Create New Project**

1. Click "New project"
2. Choose "Create blank project"
3. Project name: `everyonecook`
4. Visibility: Private (recommended)
5. Initialize with README: No (we already have code)
6. Click "Create project"

![GitLab Project Created](/images/5-Workshop/5.9-push-gitlab/gitlab-project.png)
*Screenshot: GitLab showing new project created*

**3. Get Repository URL**

```
# HTTPS URL
https://gitlab.com/your-username/everyonecook.git

# SSH URL (if you have SSH key configured)
git@gitlab.com:your-username/everyonecook.git
```

#### Step 5: Add Remote and Push

**1. Add GitLab Remote**

```bash
# Add remote (use HTTPS)
git remote add origin https://gitlab.com/your-username/everyonecook.git

# Or use SSH
git remote add origin git@gitlab.com:your-username/everyonecook.git

# Verify remote
git remote -v
```

**2. Push to GitLab**

```bash
# Push to main branch
git push -u origin main

# Enter GitLab credentials if using HTTPS
# Username: your-username
# Password: your-personal-access-token (not your password!)
```

![Git Push](/images/5-Workshop/5.9-push-gitlab/git-push.png)
*Screenshot: Terminal showing successful push to GitLab*

**3. Verify on GitLab**

Go to your GitLab repository and verify files are uploaded.

![GitLab Repository](/images/5-Workshop/5.9-push-gitlab/gitlab-repo.png)
*Screenshot: GitLab showing repository with files*

#### Step 6: Create Branch Structure

**1. Create Development Branch**

```bash
# Create and switch to dev branch
git checkout -b dev

# Push dev branch
git push -u origin dev
```

**2. Create Feature Branch**

```bash
# Create feature branch from dev
git checkout -b feature/add-notifications

# Make changes...
# Commit changes...
# Push feature branch
git push -u origin feature/add-notifications
```

**3. Branch Strategy**

```
main (production)
  ↓
dev (development)
  ↓
feature/* (features)
hotfix/* (urgent fixes)
```

#### Step 7: Configure GitLab CI/CD

**1. Create .gitlab-ci.yml**

```bash
# Create CI/CD configuration
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

# Deploy to dev
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

# Deploy to production
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

**2. Commit CI/CD Configuration**

```bash
# Add and commit
git add .gitlab-ci.yml
git commit -m "Add GitLab CI/CD configuration"
git push
```

![GitLab CI/CD](/images/5-Workshop/5.9-push-gitlab/gitlab-cicd.png)
*Screenshot: GitLab showing CI/CD pipeline*

#### Step 8: Configure GitLab Variables

**1. Add AWS Credentials**

1. Go to GitLab → Settings → CI/CD → Variables
2. Add variables:
   - `AWS_ACCESS_KEY_ID`: Your AWS access key
   - `AWS_SECRET_ACCESS_KEY`: Your AWS secret key (masked)
   - `AWS_DEFAULT_REGION`: us-east-1

![GitLab Variables](/images/5-Workshop/5.9-push-gitlab/gitlab-variables.png)
*Screenshot: GitLab CI/CD variables configuration*

**2. Add Environment Variables**

Add other environment variables:
- `DOMAIN_NAME`: everyonecook.cloud
- `ENABLE_OPENSEARCH`: false
- etc.

#### Step 9: Set Up Branch Protection

**1. Protect Main Branch**

1. Go to Settings → Repository → Protected branches
2. Select `main` branch
3. Allowed to merge: Maintainers
4. Allowed to push: No one
5. Require approval: Yes (1 approval)

**2. Protect Dev Branch**

1. Select `dev` branch
2. Allowed to merge: Developers + Maintainers
3. Allowed to push: Developers + Maintainers

#### Step 10: Create Merge Request Workflow

**1. Create Feature Branch**

```bash
# Create feature branch
git checkout dev
git pull
git checkout -b feature/new-feature

# Make changes
# ...

# Commit changes
git add .
git commit -m "Add new feature"
git push -u origin feature/new-feature
```

**2. Create Merge Request**

1. Go to GitLab → Merge requests → New merge request
2. Source branch: feature/new-feature
3. Target branch: dev
4. Title: "Add new feature"
5. Description: Describe changes
6. Assign to reviewer
7. Create merge request

**3. Review and Merge**

1. Reviewer reviews code
2. CI/CD pipeline runs tests
3. If tests pass and approved, merge to dev
4. Delete feature branch

#### Step 11: Tag Releases

**1. Create Release Tag**

```bash
# After merging to main
git checkout main
git pull

# Create tag
git tag -a v1.0.0 -m "Release version 1.0.0"

# Push tag
git push origin v1.0.0
```

**2. Create Release on GitLab**

1. Go to Repository → Tags
2. Find your tag
3. Click "Create release"
4. Add release notes
5. Publish release

#### Git Workflow Summary

```
1. Create feature branch from dev
   git checkout -b feature/new-feature

2. Make changes and commit
   git add .
   git commit -m "Description"

3. Push to GitLab
   git push -u origin feature/new-feature

4. Create merge request on GitLab
   feature/new-feature → dev

5. Review, test, and merge

6. Deploy to dev environment
   (manual trigger in GitLab CI/CD)

7. Test in dev environment

8. Merge dev → main for production

9. Deploy to production
   (manual trigger in GitLab CI/CD)

10. Tag release
    git tag -a v1.0.0 -m "Release"
```

#### Best Practices

**Commit Messages:**
```
feat: Add user authentication
fix: Fix login bug
docs: Update README
style: Format code
refactor: Refactor user service
test: Add unit tests
chore: Update dependencies
```

**Branch Naming:**
```
feature/add-notifications
bugfix/fix-login-error
hotfix/critical-security-patch
release/v1.0.0
```

#### Troubleshooting

**Issue: Authentication failed**

```bash
# Use personal access token instead of password
# Generate token: GitLab → Settings → Access Tokens
# Use token as password when pushing
```

**Issue: Large files rejected**

```bash
# Check file size
git ls-files -z | xargs -0 du -h | sort -h

# Remove large files from history
git filter-branch --tree-filter 'rm -f large-file.zip' HEAD
```

**Issue: Merge conflicts**

```bash
# Update your branch
git checkout feature/your-feature
git fetch origin
git merge origin/dev

# Resolve conflicts in files
# Then commit
git add .
git commit -m "Resolve merge conflicts"
git push
```

#### Next Steps

Once code is pushed to GitLab, proceed to [Deploy to Amplify](../5.10-deploy-amplify/) to deploy your frontend application.
