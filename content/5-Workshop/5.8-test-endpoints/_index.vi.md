---
title: "Test Endpoints End-to-End"
date: 2025-12-01
weight: 8
chapter: false
pre: " <b> 5.8. </b> "
---

#### Tổng quan

Sau khi deploy backend, bạn cần test tất cả endpoints để đảm bảo hệ thống hoạt động đúng từ đầu đến cuối.

#### Luồng Testing

```
1. Test Health Check
2. Test Đăng ký người dùng
3. Test Xác minh Email
4. Test Đăng nhập
5. Test Quản lý Profile
6. Test Tính năng Social (Posts, Comments, Likes)
7. Test Tính năng Recipe
8. Test Tính năng AI (Bedrock)
9. Test Upload File (S3 + CloudFront)
10. Test Tìm kiếm (OpenSearch)
```

#### Bước 1: Thiết lập môi trường Testing

**1. Cài đặt công cụ Testing**

```bash
# Cài đặt Postman (khuyến nghị)
# Tải từ: https://www.postman.com/downloads/

# Hoặc sử dụng curl cho command-line testing
```

**2. Đặt biến môi trường**

```bash
# Đặt API endpoint
export API_ENDPOINT="https://api.everyonecook.cloud"

# Hoặc cho PowerShell
$API_ENDPOINT = "https://api.everyonecook.cloud"
```

#### Bước 2: Test Health Check

```bash
# Test health endpoint (không cần auth)
curl $API_ENDPOINT/health

# Response mong đợi:
# {
#   "status": "healthy",
#   "timestamp": "2024-01-01T00:00:00.000Z"
# }
```

![Health Check](/images/5-Workshop/5.8-test-endpoints/health-check.png)
*Ảnh chụp: Postman hiển thị health check thành công*

#### Bước 3: Test Đăng ký người dùng

**1. Đăng ký người dùng mới**

```bash
# Lấy User Pool Client ID
CLIENT_ID=$(aws cloudformation describe-stacks \
  --stack-name EveryoneCook-dev-Auth \
  --query 'Stacks[0].Outputs[?OutputKey==`UserPoolClientId`].OutputValue' \
  --output text)

# Đăng ký user
aws cognito-idp sign-up \
  --client-id $CLIENT_ID \
  --username testuser \
  --password TestPassword123! \
  --user-attributes \
    Name=email,Value=test@example.com \
    Name=given_name,Value="Test User"
```

**Mong đợi:** Thông báo thành công với mã xác nhận gửi đến email

**2. Xác minh Pre-Signup Trigger**

```bash
# Kiểm tra CloudWatch logs cho Pre-Signup trigger
aws logs tail /aws/lambda/EveryoneCook-dev-PreSignUpTrigger --since 5m
```

#### Bước 4: Test Xác minh Email

**1. Lấy mã xác minh**

Kiểm tra email để lấy mã xác minh (6 chữ số)

**2. Xác nhận người dùng**

```bash
# Xác nhận user với mã
aws cognito-idp confirm-sign-up \
  --client-id $CLIENT_ID \
  --username testuser \
  --confirmation-code 123456
```

**3. Xác minh Post-Confirmation Trigger**

```bash
# Kiểm tra xem user profile đã được tạo trong DynamoDB chưa
aws dynamodb get-item \
  --table-name EveryoneCook-dev \
  --key '{"PK":{"S":"USER#testuser"},"SK":{"S":"PROFILE"}}'

# Phải trả về user profile với:
# - PK: USER#testuser
# - SK: PROFILE
# - userId: cognito-sub-id
# - email: test@example.com
# - fullName: Test User
# - birthday: null
# - gender: null
# - country: null
```

![User Profile đã tạo](/images/5-Workshop/5.8-test-endpoints/user-profile-created.png)
*Ảnh chụp: DynamoDB hiển thị user profile được tạo bởi trigger*

#### Bước 5: Test Đăng nhập

**1. Đăng nhập**

```bash
# Đăng nhập để lấy tokens
TOKENS=$(aws cognito-idp initiate-auth \
  --client-id $CLIENT_ID \
  --auth-flow USER_PASSWORD_AUTH \
  --auth-parameters USERNAME=testuser,PASSWORD=TestPassword123!)

# Trích xuất tokens
ACCESS_TOKEN=$(echo $TOKENS | jq -r '.AuthenticationResult.AccessToken')
ID_TOKEN=$(echo $TOKENS | jq -r '.AuthenticationResult.IdToken')
REFRESH_TOKEN=$(echo $TOKENS | jq -r '.AuthenticationResult.RefreshToken')

echo "ID Token: $ID_TOKEN"
```

**2. Xác minh Post-Authentication Trigger**

```bash
# Kiểm tra xem lastLoginAt đã được cập nhật chưa
aws dynamodb get-item \
  --table-name EveryoneCook-dev \
  --key '{"PK":{"S":"USER#testuser"},"SK":{"S":"PROFILE"}}' \
  --projection-expression "lastLoginAt"
```

#### Bước 6: Test Quản lý Profile

**1. Lấy Profile**

```bash
# Lấy user profile
curl -X GET \
  -H "Authorization: Bearer $ID_TOKEN" \
  $API_ENDPOINT/auth/profile

# Mong đợi: Dữ liệu user profile
```

**2. Cập nhật Profile**

```bash
# Cập nhật profile (onboarding)
curl -X PUT \
  -H "Authorization: Bearer $ID_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "birthday": "1990-01-01",
    "gender": "male",
    "country": "US"
  }' \
  $API_ENDPOINT/auth/profile

# Mong đợi: Profile đã cập nhật
```

**3. Xác minh cập nhật trong DynamoDB**

```bash
# Kiểm tra profile đã cập nhật
aws dynamodb get-item \
  --table-name EveryoneCook-dev \
  --key '{"PK":{"S":"USER#testuser"},"SK":{"S":"PROFILE"}}'

# Phải hiển thị birthday, gender, country đã cập nhật
```

![Profile đã cập nhật](/images/5-Workshop/5.8-test-endpoints/profile-updated.png)
*Ảnh chụp: Postman hiển thị cập nhật profile thành công*

#### Bước 7: Test Tính năng Social

**1. Tạo Post**

```bash
# Tạo một post
POST_RESPONSE=$(curl -X POST \
  -H "Authorization: Bearer $ID_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "content": "Bài đăng đầu tiên của tôi trên EveryoneCook!",
    "visibility": "public"
  }' \
  $API_ENDPOINT/social/posts)

POST_ID=$(echo $POST_RESPONSE | jq -r '.postId')
echo "Đã tạo post: $POST_ID"
```

**2. Lấy Posts Feed**

```bash
# Lấy posts
curl -X GET \
  -H "Authorization: Bearer $ID_TOKEN" \
  $API_ENDPOINT/social/posts

# Mong đợi: Mảng posts bao gồm post vừa tạo
```

**3. Like Post**

```bash
# Like post
curl -X POST \
  -H "Authorization: Bearer $ID_TOKEN" \
  $API_ENDPOINT/social/posts/$POST_ID/like

# Mong đợi: Thông báo thành công
```

**4. Comment trên Post**

```bash
# Thêm comment
curl -X POST \
  -H "Authorization: Bearer $ID_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"content":"Bài viết hay!"}' \
  $API_ENDPOINT/social/posts/$POST_ID/comment

# Mong đợi: Comment đã tạo
```

![Tính năng Social](/images/5-Workshop/5.8-test-endpoints/social-features.png)
*Ảnh chụp: Postman hiển thị tạo post và tương tác*

#### Bước 8: Test Tính năng Recipe

**1. Tạo Recipe**

```bash
# Tạo một recipe
RECIPE_RESPONSE=$(curl -X POST \
  -H "Authorization: Bearer $ID_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Phở Bò (Phở Bò Việt Nam)",
    "description": "Phở bò truyền thống Việt Nam",
    "ingredients": [
      {"name": "xương bò", "amount": "2", "unit": "kg"},
      {"name": "bánh phở", "amount": "500", "unit": "g"},
      {"name": "gừng", "amount": "1", "unit": "củ"}
    ],
    "instructions": [
      "Ninh xương bò trong 2 giờ",
      "Thêm gia vị và hầm",
      "Chuẩn bị bánh phở và dọn ra"
    ],
    "cuisine": "Vietnamese",
    "difficulty": "medium",
    "prepTime": 30,
    "cookTime": 120
  }' \
  $API_ENDPOINT/recipes)

RECIPE_ID=$(echo $RECIPE_RESPONSE | jq -r '.recipeId')
echo "Đã tạo recipe: $RECIPE_ID"
```

**2. Lấy Recipes**

```bash
# Lấy tất cả recipes
curl -X GET \
  -H "Authorization: Bearer $ID_TOKEN" \
  $API_ENDPOINT/recipes

# Mong đợi: Mảng recipes
```

**3. Lấy Recipe theo ID**

```bash
# Lấy recipe cụ thể
curl -X GET \
  -H "Authorization: Bearer $ID_TOKEN" \
  $API_ENDPOINT/recipes/$RECIPE_ID

# Mong đợi: Chi tiết recipe
```

#### Bước 9: Test Tính năng AI

**1. Tạo Recipe với AI**

```bash
# Tạo recipe sử dụng Bedrock
curl -X POST \
  -H "Authorization: Bearer $ID_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "ingredients": ["gà", "cơm", "rau"],
    "cuisine": "Vietnamese",
    "dietaryRestrictions": ["gluten-free"],
    "servings": 4
  }' \
  $API_ENDPOINT/ai/generate-recipe

# Mong đợi: Recipe do AI tạo (mất 5-10 giây)
# Response bao gồm tên nguyên liệu tiếng Việt
```

![Tạo Recipe AI](/images/5-Workshop/5.8-test-endpoints/ai-recipe.png)
*Ảnh chụp: Postman hiển thị recipe do AI tạo với tên tiếng Việt*

**2. Dịch nguyên liệu**

```bash
# Dịch nguyên liệu sang tiếng Việt
curl -X POST \
  -H "Authorization: Bearer $ID_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "ingredient": "tomato",
    "targetLanguage": "vi"
  }' \
  $API_ENDPOINT/ai/translate

# Mong đợi: {"translation": "cà chua", "confidence": 0.99}
```

#### Bước 10: Test Upload File

**1. Yêu cầu Pre-signed URL**

```bash
# Lấy pre-signed URL cho avatar upload
UPLOAD_RESPONSE=$(curl -X POST \
  -H "Authorization: Bearer $ID_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "fileType": "avatar",
    "fileName": "avatar.jpg",
    "contentType": "image/jpeg",
    "fileSize": 1024000
  }' \
  $API_ENDPOINT/upload/presigned-url)

PRESIGNED_URL=$(echo $UPLOAD_RESPONSE | jq -r '.url')
UPLOAD_KEY=$(echo $UPLOAD_RESPONSE | jq -r '.key')

echo "Pre-signed URL: $PRESIGNED_URL"
echo "Upload Key: $UPLOAD_KEY"
```

**2. Upload File lên S3**

```bash
# Tạo test image
echo "Test image content" > test-avatar.jpg

# Upload sử dụng pre-signed URL
curl -X PUT \
  -H "Content-Type: image/jpeg" \
  --upload-file test-avatar.jpg \
  "$PRESIGNED_URL"

# Mong đợi: 200 OK
```

**3. Đánh dấu Upload hoàn tất**

```bash
# Thông báo backend rằng upload đã hoàn tất
curl -X POST \
  -H "Authorization: Bearer $ID_TOKEN" \
  -H "Content-Type: application/json" \
  -d "{\"key\":\"$UPLOAD_KEY\"}" \
  $API_ENDPOINT/upload/complete

# Mong đợi: Thông báo thành công
```

**4. Truy cập qua CloudFront**

```bash
# Truy cập file qua CloudFront CDN
curl -I https://cdn.everyonecook.cloud/$UPLOAD_KEY

# Request đầu tiên: X-Cache: Miss from cloudfront
# Request thứ hai: X-Cache: Hit from cloudfront
```

![Upload File](/images/5-Workshop/5.8-test-endpoints/file-upload.png)
*Ảnh chụp: CloudFront cache headers hiển thị Miss rồi Hit*

#### Bước 11: Test Tìm kiếm (OpenSearch)

**Nếu OpenSearch được bật:**

```bash
# Tìm kiếm recipes với query tiếng Việt
curl -X POST \
  -H "Authorization: Bearer $ID_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "query": "phở bò",
    "filters": {
      "cuisine": "Vietnamese",
      "difficulty": "medium"
    },
    "limit": 10
  }' \
  $API_ENDPOINT/ai/search

# Mong đợi: Mảng recipes khớp
# Vietnamese analyzer xử lý: "phở bò" = "pho bo" = "beef noodle soup"
```

#### Bước 12: Test Tính năng Admin

**1. Liệt kê Users (Chỉ Admin)**

```bash
# Lấy tất cả users (yêu cầu admin role)
curl -X GET \
  -H "Authorization: Bearer $ID_TOKEN" \
  $API_ENDPOINT/admin/users

# Mong đợi: Mảng users hoặc 403 Forbidden nếu không phải admin
```

#### Bước 13: Xác minh Async Processing

**1. Kiểm tra SQS Queue Processing**

```bash
# Gửi message đến SearchIndex queue
QUEUE_URL=$(aws sqs list-queues \
  --queue-name-prefix EveryoneCook-dev-SearchIndexQueue \
  | jq -r '.QueueUrls[0]')

aws sqs send-message \
  --queue-url $QUEUE_URL \
  --message-body "{
    \"eventName\": \"INSERT\",
    \"tableName\": \"recipes\",
    \"keys\": {\"PK\": \"USER#testuser\", \"SK\": \"RECIPE#$RECIPE_ID\"},
    \"newImage\": {
      \"title\": \"Pho Bo\",
      \"ingredients\": [\"beef\", \"noodles\"],
      \"cuisine\": \"Vietnamese\"
    }
  }"

# Kiểm tra worker logs
aws logs tail /aws/lambda/EveryoneCook-dev-SearchSyncWorker --follow
```

#### Checklist Testing

- [ ] Health check phản hồi
- [ ] Đăng ký user hoạt động
- [ ] Xác minh email hoạt động
- [ ] Đăng nhập thành công
- [ ] Các thao tác CRUD profile hoạt động
- [ ] Có thể tạo posts
- [ ] Có thể like posts
- [ ] Có thể thêm comments
- [ ] Có thể tạo recipes
- [ ] Tạo recipe AI hoạt động
- [ ] Dịch nguyên liệu hoạt động
- [ ] Upload file lên S3 hoạt động
- [ ] CloudFront phục vụ files
- [ ] Tìm kiếm hoạt động (nếu OpenSearch bật)
- [ ] SQS queues xử lý messages
- [ ] Tất cả Lambda functions thực thi không có lỗi

#### Benchmarks hiệu suất

**Thời gian phản hồi mong đợi:**
- Health check: < 50ms
- Đăng nhập: < 200ms
- Lấy profile: < 100ms
- Tạo post: < 300ms
- Lấy posts feed: < 500ms
- Tạo recipe AI: 5-10 giây
- Upload file (pre-signed URL): < 100ms
- Truy vấn tìm kiếm: < 200ms

#### Bước tiếp theo

Sau khi tất cả tests pass, tiến hành [Push lên GitLab](../5.9-push-gitlab/) để version control code và thiết lập CI/CD.
