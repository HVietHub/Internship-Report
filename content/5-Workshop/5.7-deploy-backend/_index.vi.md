---
title: "Deploy Backend Services"
date: 2025-12-01
weight: 7
chapter: false
pre: " <b> 5.7. </b> "
---

#### Tổng quan

Sau khi cấu hình xong, bạn sẽ deploy Lambda code lên AWS. Quá trình này bao gồm compile TypeScript, cài đặt dependencies, và cập nhật Lambda functions.

#### Quy trình Deployment

```
1. Compile TypeScript → JavaScript
2. Cài đặt production dependencies
3. Tạo deployment packages
4. Cập nhật Lambda functions
5. Xác minh deployments
6. Kiểm tra CloudWatch logs
```

#### Bước 1: Chuẩn bị Backend Deployment

**1. Chạy Script chuẩn bị**

```bash
# Di chuyển đến project root
cd /path/to/everyonecook-dev

# Chạy script chuẩn bị (PowerShell)
.\prepare-backend-deployment.ps1
```

**Các hành động của Script:**
- Compile TypeScript sang JavaScript cho tất cả modules
- Cài đặt production dependencies (không có devDependencies)
- Tạo deployment packages
- Đảm bảo CloudFormation phát hiện thay đổi

![Chuẩn bị Backend](/images/5-Workshop/5.7-deploy-backend/backend-prep.png)
*Ảnh chụp: Terminal hiển thị script chuẩn bị đang chạy*

**2. Xác minh chuẩn bị**

```bash
# Chạy script xác minh
.\verify-deployment.ps1

# Kiểm tra:
# - Tất cả modules có thư mục dist/
# - Tất cả modules có node_modules/
# - Tất cả modules có package.json
# - Deployment packages đã sẵn sàng
```

#### Bước 2: Deploy Lambda Functions

**Lựa chọn A: Deploy tất cả Functions (Khuyến nghị)**

```bash
# Di chuyển đến infrastructure
cd infrastructure

# Deploy Backend stack (cập nhật tất cả Lambdas)
npx cdk deploy EveryoneCook-dev-Backend --context environment=dev

# Gõ 'y' để xác nhận
```

**Lựa chọn B: Deploy từng Function riêng lẻ**

```bash
# Cập nhật Lambda function cụ thể
aws lambda update-function-code \
  --function-name EveryoneCook-dev-AuthModule \
  --zip-file fileb://services/auth-module/deployment/lambda.zip

# Chờ cập nhật hoàn tất
aws lambda wait function-updated \
  --function-name EveryoneCook-dev-AuthModule
```

![Deploy Lambda](/images/5-Workshop/5.7-deploy-backend/lambda-deploy.png)
*Ảnh chụp: Terminal hiển thị Lambda functions đang được cập nhật*

#### Bước 3: Xác minh Lambda Deployments

**1. Kiểm tra trạng thái Function**

```bash
# Liệt kê tất cả Lambda functions
aws lambda list-functions \
  --query 'Functions[?contains(FunctionName, `EveryoneCook-dev`)].{Name:FunctionName,Runtime:Runtime,Updated:LastModified}' \
  --output table
```

**2. Kiểm tra cấu hình Function**

```bash
# Lấy chi tiết Auth Module
aws lambda get-function \
  --function-name EveryoneCook-dev-AuthModule \
  --query '{Runtime:Configuration.Runtime,Handler:Configuration.Handler,Timeout:Configuration.Timeout,Memory:Configuration.MemorySize}'
```

**3. Xác minh Code SHA256**

```bash
# Kiểm tra code hash (phải khác sau khi cập nhật)
aws lambda get-function \
  --function-name EveryoneCook-dev-AuthModule \
  --query 'Configuration.CodeSha256'
```

#### Bước 4: Test Lambda Functions

**1. Test API Router**

```bash
# Gọi API Router với test event
aws lambda invoke \
  --function-name EveryoneCook-dev-APIRouter \
  --payload '{"httpMethod":"GET","path":"/health","headers":{}}' \
  response.json

# Kiểm tra response
cat response.json
# Phải trả về: {"statusCode":200,"body":"{\"status\":\"healthy\"}"}
```

**2. Test Auth Module**

```bash
# Test get profile (yêu cầu token hợp lệ)
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

#### Bước 5: Kiểm tra CloudWatch Logs

**1. Xem Logs gần đây**

```bash
# Theo dõi Auth Module logs
aws logs tail /aws/lambda/EveryoneCook-dev-AuthModule --follow

# Hoặc xem 10 phút gần nhất
aws logs tail /aws/lambda/EveryoneCook-dev-AuthModule --since 10m
```

**2. Tìm kiếm Errors**

```bash
# Tìm errors trong giờ qua
aws logs filter-log-events \
  --log-group-name /aws/lambda/EveryoneCook-dev-AuthModule \
  --start-time $(date -d '1 hour ago' +%s)000 \
  --filter-pattern "ERROR"
```

**3. Kiểm tra Lambda Insights**

```bash
# Lấy Lambda metrics
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
*Ảnh chụp: CloudWatch Logs hiển thị Lambda execution*

#### Bước 6: Deploy Lambda Triggers

**Lambda triggers đã được deploy với Auth Stack, xác minh:**

```bash
# Kiểm tra Post-Confirmation trigger
aws lambda get-function \
  --function-name EveryoneCook-dev-PostConfirmationTrigger

# Test trigger (sẽ được gọi tự động khi user confirmation)
```

#### Bước 7: Deploy Search Sync Worker

**1. Deploy Worker**

```bash
# Worker được deploy với Backend Stack
# Xác minh deployment
aws lambda get-function \
  --function-name EveryoneCook-dev-SearchSyncWorker
```

**2. Test Worker với SQS**

```bash
# Lấy SearchIndex queue URL
QUEUE_URL=$(aws sqs list-queues \
  --queue-name-prefix EveryoneCook-dev-SearchIndexQueue \
  | jq -r '.QueueUrls[0]')

# Gửi test message
aws sqs send-message \
  --queue-url $QUEUE_URL \
  --message-body '{
    "eventName": "INSERT",
    "tableName": "recipes",
    "keys": {"PK": "USER#testuser", "SK": "RECIPE#test-123"},
    "newImage": {"title": "Test Recipe", "cuisine": "Vietnamese"}
  }'

# Kiểm tra worker logs
aws logs tail /aws/lambda/EveryoneCook-dev-SearchSyncWorker --follow
```

#### Bước 8: Cập nhật API Gateway

**API Gateway tự động cập nhật khi deploy Backend Stack, xác minh:**

```bash
# Lấy API Gateway deployment
API_ID=$(aws cloudformation describe-stacks \
  --stack-name EveryoneCook-dev-Backend \
  --query 'Stacks[0].Outputs[?OutputKey==`ApiId`].OutputValue' \
  --output text)

# Liệt kê deployments
aws apigateway get-deployments --rest-api-id $API_ID

# Lấy deployment mới nhất
aws apigateway get-deployment \
  --rest-api-id $API_ID \
  --deployment-id $(aws apigateway get-deployments \
    --rest-api-id $API_ID \
    --query 'items[0].id' \
    --output text)
```

#### Bước 9: Warm Up Lambda Functions

**Tránh cold start cho requests đầu tiên:**

```bash
# Gọi tất cả functions một lần
for func in APIRouter AuthModule SocialModule RecipeAIModule AdminModule UploadModule SearchSyncWorker; do
  echo "Warming up $func..."
  aws lambda invoke \
    --function-name EveryoneCook-dev-$func \
    --payload '{"warmup":true}' \
    /dev/null
done
```

#### Bước 10: Xác minh End-to-End

**1. Test Health Endpoint**

```bash
# Test qua API Gateway
curl https://api.everyonecook.cloud/health

# Phải trả về: {"status":"healthy","timestamp":"..."}
```

**2. Test với Postman**

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

#### Checklist Deployment

- [ ] Script chuẩn bị hoàn thành thành công
- [ ] Tất cả Lambda functions đã cập nhật
- [ ] Trạng thái function: Active
- [ ] Test invocations thành công
- [ ] CloudWatch logs hiển thị executions
- [ ] Không có errors trong logs
- [ ] Lambda triggers hoạt động
- [ ] Search Sync Worker xử lý messages
- [ ] API Gateway đã cập nhật
- [ ] Health endpoint phản hồi
- [ ] Functions đã warm up

#### Xử lý sự cố

**Vấn đề: Lambda update thất bại**

```bash
# Kiểm tra trạng thái function
aws lambda get-function \
  --function-name EveryoneCook-dev-AuthModule \
  --query 'Configuration.State'

# Nếu state là "Failed", kiểm tra StateReasonCode
aws lambda get-function \
  --function-name EveryoneCook-dev-AuthModule \
  --query 'Configuration.StateReasonCode'
```

**Vấn đề: Function timeout**

```bash
# Tăng timeout
aws lambda update-function-configuration \
  --function-name EveryoneCook-dev-AuthModule \
  --timeout 30
```

**Vấn đề: Hết memory**

```bash
# Tăng memory
aws lambda update-function-configuration \
  --function-name EveryoneCook-dev-AuthModule \
  --memory-size 512
```

**Vấn đề: Thiếu biến môi trường**

```bash
# Cập nhật biến môi trường
aws lambda update-function-configuration \
  --function-name EveryoneCook-dev-AuthModule \
  --environment Variables={TABLE_NAME=EveryoneCook-dev,REGION=us-east-1}
```

#### Theo dõi hiệu suất

**Kiểm tra Lambda metrics:**

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

#### Bước tiếp theo

Sau khi backend đã deploy và xác minh, tiến hành [Test Endpoints End-to-End](../5.8-test-endpoints/) để test toàn bộ luồng ứng dụng.
