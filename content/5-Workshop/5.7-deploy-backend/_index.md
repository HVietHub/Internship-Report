---
title: "Deploy Backend Services"
date: 2025-12-01
weight: 7
chapter: false
pre: " <b> 5.7. </b> "
---

#### Overview

Sau khi cấu hình xong, bạn sẽ deploy Lambda code lên AWS. Quá trình này bao gồm compile TypeScript, install dependencies, và update Lambda functions.

#### Deployment Process

```
1. Compile TypeScript → JavaScript
2. Install production dependencies
3. Create deployment packages
4. Update Lambda functions
5. Verify deployments
6. Check CloudWatch logs
```

#### Step 1: Prepare Backend Deployment

**1. Run Preparation Script**

```bash
# Navigate to project root
cd /path/to/everyonecook-dev

# Run preparation script (PowerShell)
.\prepare-backend-deployment.ps1
```

**Script Actions:**
- Compiles TypeScript to JavaScript for all modules
- Installs production dependencies (no devDependencies)
- Creates deployment packages
- Ensures CloudFormation detects changes

![Backend Preparation](/images/5-Workshop/5.7-deploy-backend/backend-prep.png)
*Screenshot: Terminal showing preparation script running*

**2. Verify Preparation**

```bash
# Run verification script
.\verify-deployment.ps1

# Checks:
# - All modules have dist/ folder
# - All modules have node_modules/
# - All modules have package.json
# - Deployment packages are ready
```

#### Step 2: Deploy Lambda Functions

**Option A: Deploy All Functions (Recommended)**

```bash
# Navigate to infrastructure
cd infrastructure

# Deploy Backend stack (updates all Lambdas)
npx cdk deploy EveryoneCook-dev-Backend --context environment=dev

# Type 'y' to confirm
```

**Option B: Deploy Individual Functions**

```bash
# Update specific Lambda function
aws lambda update-function-code \
  --function-name EveryoneCook-dev-AuthModule \
  --zip-file fileb://services/auth-module/deployment/lambda.zip

# Wait for update to complete
aws lambda wait function-updated \
  --function-name EveryoneCook-dev-AuthModule
```

![Lambda Deploy](/images/5-Workshop/5.7-deploy-backend/lambda-deploy.png)
*Screenshot: Terminal showing Lambda functions being updated*

#### Step 3: Verify Lambda Deployments

**1. Check Function Status**

```bash
# List all Lambda functions
aws lambda list-functions \
  --query 'Functions[?contains(FunctionName, `EveryoneCook-dev`)].{Name:FunctionName,Runtime:Runtime,Updated:LastModified}' \
  --output table
```

**2. Check Function Configuration**

```bash
# Get Auth Module details
aws lambda get-function \
  --function-name EveryoneCook-dev-AuthModule \
  --query '{Runtime:Configuration.Runtime,Handler:Configuration.Handler,Timeout:Configuration.Timeout,Memory:Configuration.MemorySize}'
```

**3. Verify Code SHA256**

```bash
# Check code hash (should be different after update)
aws lambda get-function \
  --function-name EveryoneCook-dev-AuthModule \
  --query 'Configuration.CodeSha256'
```

#### Step 4: Test Lambda Functions

**1. Test API Router**

```bash
# Invoke API Router with test event
aws lambda invoke \
  --function-name EveryoneCook-dev-APIRouter \
  --payload '{"httpMethod":"GET","path":"/health","headers":{}}' \
  response.json

# Check response
cat response.json
# Should return: {"statusCode":200,"body":"{\"status\":\"healthy\"}"}
```

**2. Test Auth Module**

```bash
# Test get profile (requires valid token)
aws lambda invoke \
  --function-name EveryoneCook-dev-AuthModule \
  --payload '{
    "httpMethod":"GET",
    "path":"/auth/profile",
    "headers":{"Authorization":"Bearer test-token"},
    "requestContext":{"authorizer":{"claims":{"sub":"test-user-id","username":"testuser"}}}
  }' \
  response.json

cat response.json
```

**3. Test Social Module**

```bash
# Test get posts
aws lambda invoke \
  --function-name EveryoneCook-dev-SocialModule \
  --payload '{
    "httpMethod":"GET",
    "path":"/social/posts",
    "headers":{"Authorization":"Bearer test-token"},
    "requestContext":{"authorizer":{"claims":{"sub":"test-user-id","username":"testuser"}}}
  }' \
  response.json

cat response.json
```

#### Step 5: Check CloudWatch Logs

**1. View Recent Logs**

```bash
# Tail Auth Module logs
aws logs tail /aws/lambda/EveryoneCook-dev-AuthModule --follow

# Or view last 10 minutes
aws logs tail /aws/lambda/EveryoneCook-dev-AuthModule --since 10m
```

**2. Search for Errors**

```bash
# Search for errors in last hour
aws logs filter-log-events \
  --log-group-name /aws/lambda/EveryoneCook-dev-AuthModule \
  --start-time $(date -d '1 hour ago' +%s)000 \
  --filter-pattern "ERROR"
```

**3. Check Lambda Insights**

```bash
# Get Lambda metrics
aws cloudwatch get-metric-statistics \
  --namespace AWS/Lambda \
  --metric-name Invocations \
  --dimensions Name=FunctionName,Value=EveryoneCook-dev-AuthModule \
  --start-time $(date -u -d '1 hour ago' +%Y-%m-%dT%H:%M:%S) \
  --end-time $(date -u +%Y-%m-%dT%H:%M:%S) \
  --period 300 \
  --statistics Sum
```

![CloudWatch Logs](/images/5-Workshop/5.7-deploy-backend/cloudwatch-logs.png)
*Screenshot: CloudWatch Logs showing Lambda execution*

#### Step 6: Deploy Lambda Triggers

**Lambda triggers đã được deploy với Auth Stack, verify:**

```bash
# Check Post-Confirmation trigger
aws lambda get-function \
  --function-name EveryoneCook-dev-PostConfirmationTrigger

# Test trigger (will be invoked automatically on user confirmation)
```

#### Step 7: Deploy Search Sync Worker

**1. Deploy Worker**

```bash
# Worker được deploy với Backend Stack
# Verify deployment
aws lambda get-function \
  --function-name EveryoneCook-dev-SearchSyncWorker
```

**2. Test Worker with SQS**

```bash
# Get SearchIndex queue URL
QUEUE_URL=$(aws sqs list-queues \
  --queue-name-prefix EveryoneCook-dev-SearchIndexQueue \
  | jq -r '.QueueUrls[0]')

# Send test message
aws sqs send-message \
  --queue-url $QUEUE_URL \
  --message-body '{
    "eventName": "INSERT",
    "tableName": "recipes",
    "keys": {"PK": "USER#testuser", "SK": "RECIPE#test-123"},
    "newImage": {"title": "Test Recipe", "cuisine": "Vietnamese"}
  }'

# Check worker logs
aws logs tail /aws/lambda/EveryoneCook-dev-SearchSyncWorker --follow
```

#### Step 8: Update API Gateway

**API Gateway tự động update khi deploy Backend Stack, verify:**

```bash
# Get API Gateway deployment
API_ID=$(aws cloudformation describe-stacks \
  --stack-name EveryoneCook-dev-Backend \
  --query 'Stacks[0].Outputs[?OutputKey==`ApiId`].OutputValue' \
  --output text)

# List deployments
aws apigateway get-deployments --rest-api-id $API_ID

# Get latest deployment
aws apigateway get-deployment \
  --rest-api-id $API_ID \
  --deployment-id $(aws apigateway get-deployments \
    --rest-api-id $API_ID \
    --query 'items[0].id' \
    --output text)
```

#### Step 9: Warm Up Lambda Functions

**Tránh cold start cho requests đầu tiên:**

```bash
# Invoke all functions once
for func in APIRouter AuthModule SocialModule RecipeAIModule AdminModule UploadModule SearchSyncWorker; do
  echo "Warming up $func..."
  aws lambda invoke \
    --function-name EveryoneCook-dev-$func \
    --payload '{"warmup":true}' \
    /dev/null
done
```

#### Step 10: Verify End-to-End

**1. Test Health Endpoint**

```bash
# Test via API Gateway
curl https://api.everyonecook.cloud/health

# Should return: {"status":"healthy","timestamp":"..."}
```

**2. Test with Postman**

Import Postman collection:
```json
{
  "info": {
    "name": "EveryoneCook API",
    "schema": "https://schema.getpostman.com/json/collection/v2.1.0/collection.json"
  },
  "item": [
    {
      "name": "Health Check",
      "request": {
        "method": "GET",
        "url": "https://api.everyonecook.cloud/health"
      }
    }
  ]
}
```

#### Deployment Checklist

- [ ] Preparation script completed successfully
- [ ] All Lambda functions updated
- [ ] Function status: Active
- [ ] Test invocations successful
- [ ] CloudWatch logs showing executions
- [ ] No errors in logs
- [ ] Lambda triggers working
- [ ] Search Sync Worker processing messages
- [ ] API Gateway updated
- [ ] Health endpoint responding
- [ ] Functions warmed up

#### Troubleshooting

**Issue: Lambda update fails**

```bash
# Check function state
aws lambda get-function \
  --function-name EveryoneCook-dev-AuthModule \
  --query 'Configuration.State'

# If state is "Failed", check StateReasonCode
aws lambda get-function \
  --function-name EveryoneCook-dev-AuthModule \
  --query 'Configuration.StateReasonCode'
```

**Issue: Function timeout**

```bash
# Increase timeout
aws lambda update-function-configuration \
  --function-name EveryoneCook-dev-AuthModule \
  --timeout 30
```

**Issue: Out of memory**

```bash
# Increase memory
aws lambda update-function-configuration \
  --function-name EveryoneCook-dev-AuthModule \
  --memory-size 512
```

**Issue: Environment variables missing**

```bash
# Update environment variables
aws lambda update-function-configuration \
  --function-name EveryoneCook-dev-AuthModule \
  --environment Variables={TABLE_NAME=EveryoneCook-dev,REGION=us-east-1}
```

#### Performance Monitoring

**Check Lambda metrics:**

```bash
# Invocations
aws cloudwatch get-metric-statistics \
  --namespace AWS/Lambda \
  --metric-name Invocations \
  --dimensions Name=FunctionName,Value=EveryoneCook-dev-AuthModule \
  --start-time $(date -u -d '1 hour ago' +%Y-%m-%dT%H:%M:%S) \
  --end-time $(date -u +%Y-%m-%dT%H:%M:%S) \
  --period 300 \
  --statistics Sum

# Errors
aws cloudwatch get-metric-statistics \
  --namespace AWS/Lambda \
  --metric-name Errors \
  --dimensions Name=FunctionName,Value=EveryoneCook-dev-AuthModule \
  --start-time $(date -u -d '1 hour ago' +%Y-%m-%dT%H:%M:%S) \
  --end-time $(date -u +%Y-%m-%dT%H:%M:%S) \
  --period 300 \
  --statistics Sum

# Duration
aws cloudwatch get-metric-statistics \
  --namespace AWS/Lambda \
  --metric-name Duration \
  --dimensions Name=FunctionName,Value=EveryoneCook-dev-AuthModule \
  --start-time $(date -u -d '1 hour ago' +%Y-%m-%dT%H:%M:%S) \
  --end-time $(date -u +%Y-%m-%dT%H:%M:%S) \
  --period 300 \
  --statistics Average,Maximum
```

#### Next Steps

Once backend is deployed and verified, proceed to [Test Endpoints End-to-End](../5.8-test-endpoints/) to test the complete application flow.
