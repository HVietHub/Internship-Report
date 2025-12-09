---
title: "Deploy to Amplify"
date: 2025-12-01
weight: 10
chapter: false
pre: " <b> 5.10. </b> "
---

#### Overview

Bước cuối cùng là deploy frontend Next.js 15 lên AWS Amplify với tích hợp GitLab để tự động deploy khi có code mới.

#### Step 1: Prepare Frontend Code

**1. Check Frontend Structure**

```bash
cd frontend

# Check package.json
cat package.json

# Should show Next.js 15
```

**2. Test Frontend Locally**

```bash
# Install dependencies
npm install

# Run development server
npm run dev

# Open http://localhost:3000
```

**3. Build Frontend**

```bash
# Build for production
npm run build

# Test production build
npm start
```

#### Step 2: Create Amplify App

**1. Go to AWS Amplify Console**

1. Open AWS Console
2. Search for "Amplify"
3. Click "Get started" or "New app"

**2. Connect to GitLab**

1. Choose "Host web app"
2. Select "GitLab"
3. Click "Connect branch"
4. Authorize AWS Amplify to access GitLab

![Amplify GitLab Connection](/images/5-Workshop/5.10-deploy-amplify/amplify-gitlab-connect.png)
*Screenshot: Amplify showing GitLab connection*

**3. Select Repository**

1. Choose repository: `everyonecook`
2. Choose branch: `main` (or `dev` for development)
3. Click "Next"

#### Step 3: Configure Build Settings

**1. App Name**

```
App name: everyonecook-frontend
```

**2. Build Settings**

Amplify auto-detects Next.js, but verify:

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
*Screenshot: Amplify build settings for Next.js*

**3. Advanced Settings**

Add environment variables:
```
NEXT_PUBLIC_API_URL=https://api.everyonecook.cloud
NEXT_PUBLIC_CDN_URL=https://cdn.everyonecook.cloud
NEXT_PUBLIC_USER_POOL_ID=us-east-1_ABC123
NEXT_PUBLIC_USER_POOL_CLIENT_ID=abc123def456
NEXT_PUBLIC_REGION=us-east-1
```

![Amplify Environment Variables](/images/5-Workshop/5.10-deploy-amplify/amplify-env-vars.png)
*Screenshot: Amplify environment variables configuration*

#### Step 4: Deploy Frontend

**1. Start Deployment**

Click "Save and deploy"

Amplify will:
1. Clone repository from GitLab
2. Install dependencies
3. Build Next.js app
4. Deploy to CDN
5. Provision domain

**2. Monitor Deployment**

Watch deployment progress:
- Provision
- Build
- Deploy
- Verify

![Amplify Deployment](/images/5-Workshop/5.10-deploy-amplify/amplify-deployment.png)
*Screenshot: Amplify showing deployment in progress*

**3. Wait for Completion**

Deployment takes 5-10 minutes.

![Amplify Deployment Success](/images/5-Workshop/5.10-deploy-amplify/amplify-success.png)
*Screenshot: Amplify showing successful deployment*

#### Step 5: Configure Custom Domain

**1. Add Custom Domain**

1. Go to Amplify app → Domain management
2. Click "Add domain"
3. Enter domain: `everyonecook.cloud`
4. Amplify will auto-configure:
   - Root domain: `everyonecook.cloud`
   - WWW subdomain: `www.everyonecook.cloud`

**2. DNS Configuration**

Amplify automatically creates DNS records in Route 53:
```
everyonecook.cloud → A record → Amplify
www.everyonecook.cloud → CNAME → Amplify
```

**3. SSL Certificate**

Amplify automatically provisions SSL certificate via ACM.

**4. Wait for DNS Propagation**

Takes 5-15 minutes.

![Amplify Custom Domain](/images/5-Workshop/5.10-deploy-amplify/amplify-custom-domain.png)
*Screenshot: Amplify showing custom domain configured*

#### Step 6: Verify Deployment

**1. Access Frontend**

```bash
# Via Amplify domain
curl -I https://main.d1234567890.amplifyapp.com

# Via custom domain
curl -I https://everyonecook.cloud

# Should return 200 OK
```

**2. Test Frontend Features**

1. Open https://everyonecook.cloud in browser
2. Test user registration
3. Test login
4. Test creating posts
5. Test creating recipes
6. Test AI features

![Frontend Live](/images/5-Workshop/5.10-deploy-amplify/frontend-live.png)
*Screenshot: Browser showing EveryoneCook frontend live*

#### Step 7: Configure Auto-Deploy

**1. Enable Auto-Deploy**

Auto-deploy is enabled by default. When you push to GitLab:
```bash
git push origin main
```

Amplify automatically:
1. Detects new commit
2. Starts build
3. Deploys new version
4. Updates live site

**2. Configure Branch Deployments**

Set up multiple environments:

1. Main branch → Production (everyonecook.cloud)
2. Dev branch → Development (dev.everyonecook.cloud)

Add dev branch:
1. Go to Amplify → App settings → General
2. Click "Connect branch"
3. Select `dev` branch
4. Configure subdomain: `dev.everyonecook.cloud`

![Amplify Branch Deployments](/images/5-Workshop/5.10-deploy-amplify/amplify-branches.png)
*Screenshot: Amplify showing multiple branch deployments*

#### Step 8: Configure Build Notifications

**1. Add SNS Notifications**

1. Go to Amplify → Notifications
2. Click "Add notification"
3. Select events:
   - Build started
   - Build succeeded
   - Build failed
4. Enter email address
5. Save

**2. Slack Integration (Optional)**

1. Create Slack webhook
2. Go to Amplify → Notifications
3. Add webhook URL
4. Test notification

#### Step 9: Monitor Performance

**1. Check Amplify Metrics**

1. Go to Amplify → Monitoring
2. View metrics:
   - Requests
   - Data transfer
   - Errors
   - Latency

**2. Enable CloudWatch Logs**

```bash
# View Amplify build logs
aws amplify get-job \
  --app-id d1234567890 \
  --branch-name main \
  --job-id 1
```

#### Step 10: Configure Redirects and Rewrites

**1. Add Redirects**

Create `amplify.yml` in frontend root:

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

**2. Commit and Push**

```bash
git add amplify.yml
git commit -m "Add Amplify configuration"
git push origin main
```

#### Deployment Summary

**Deployed Resources:**
- ✅ Amplify App
- ✅ Frontend hosted on Amplify CDN
- ✅ Custom domain configured
- ✅ SSL certificate provisioned
- ✅ Auto-deploy from GitLab enabled
- ✅ Multiple branch deployments
- ✅ Build notifications configured

**URLs:**
- Production: https://everyonecook.cloud
- Development: https://dev.everyonecook.cloud (if configured)
- Amplify: https://main.d1234567890.amplifyapp.com

#### Cost

**Amplify Hosting:**
- Build minutes: First 1,000 minutes/month free
- Hosting: First 15 GB served/month free
- After free tier: $0.01 per build minute, $0.15 per GB served

**Estimated Cost:**
- Dev environment: $0-5/month
- Production (low traffic): $5-20/month
- Production (high traffic): $20-100/month

#### Troubleshooting

**Issue: Build fails**

```bash
# Check build logs in Amplify console
# Common issues:
# - Missing environment variables
# - Node version mismatch
# - Build command incorrect
```

**Issue: Custom domain not working**

```bash
# Check DNS propagation
dig everyonecook.cloud

# Check Route 53 records
aws route53 list-resource-record-sets \
  --hosted-zone-id YOUR-ZONE-ID
```

**Issue: Environment variables not working**

```bash
# Ensure variables start with NEXT_PUBLIC_ for client-side
# Redeploy after adding variables
```

#### Best Practices

1. **Use Environment Variables**: Never hardcode API URLs
2. **Enable Auto-Deploy**: Automatic deployments on push
3. **Multiple Environments**: Separate dev and prod
4. **Monitor Performance**: Use Amplify metrics
5. **Set Up Notifications**: Get alerted on build failures
6. **Use Custom Domain**: Professional appearance
7. **Enable HTTPS**: Always use SSL
8. **Configure Headers**: Security headers for protection

#### Next Steps

Congratulations! Your application is now fully deployed:
- ✅ Infrastructure on AWS
- ✅ Backend APIs running
- ✅ Frontend on Amplify
- ✅ Code on GitLab
- ✅ CI/CD configured

Proceed to [Cleanup](../5.11-cleanup/) when you're done testing, or start using your application!
