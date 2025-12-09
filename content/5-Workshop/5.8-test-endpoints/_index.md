---
title: "Test Endpoints End-to-End"
date: 2025-12-01
weight: 8
chapter: false
pre: " <b> 5.8. </b> "
---

#### Overview

Sau khi deploy backend, bạn cần test tất cả endpoints để đảm bảo hệ thống hoạt động đúng từ đầu đến cuối.

#### Testing Flow

```
1. Test Health Check
2. Test User Registration
3. Test Email Verification
4. Test User Login
5. Test Profile Management
6. Test Social Features (Posts, Comments, Likes)
7. Test Recipe Features
8. Test AI Features (Bedrock)
9. Test File Upload (S3 + CloudFront)
10. Test Search (OpenSearch)
```

#### Step 1: Setup Testing Environment

**1. Install Testing Tools**

```bash
# Install Postman (recommended)
# Download from: https://www.postman.com/downloads/

# Or use curl for command-line testing
```

**2. Set Environment Variables**

```bash
# Set API endpoint
export API_ENDPOINT="https://api.everyonecook.cloud"

# Or for PowerShell
$API_ENDPOINT = "https://api.everyonecook.cloud"
```

#### Step 2: Test Health Check

```bash
# Test health endpoint (no auth required)
curl $API_ENDPOINT/health

# Expected response:
# {
#   "status": "healthy",
#   "timestamp": "2024-01-01T00:00:00.000Z"
# }
```

![Health Check](/images/5-Workshop/5.8-test-endpoints/health-check.png)
*Screenshot: Postman showing successful health check*

#### Step 3: Test User Registration

**1. Register New User**

```bash
# Get User Pool Client ID
CLIENT_ID=$(aws cloudformation describe-stacks \
  --stack-name EveryoneCook-dev-Auth \
  --query 'Stacks[0].Outputs[?OutputKey==`UserPoolClientId`].OutputValue' \
  --output text)

# Register user
aws cognito-idp sign-up \
  --client-id $CLIENT_ID \
  --username testuser \
  --password TestPassword123! \
  --user-attributes \
    Name=email,Value=test@example.com \
    Name=given_name,Value="Test User"
```

**Expected:** Success message with confirmation code sent to email

**2. Verify Pre-Signup Trigger**

```bash
# Check CloudWatch logs for Pre-Signup trigger
aws logs tail /aws/lambda/EveryoneCook-dev-PreSignUpTrigger --since 5m
```

#### Step 4: Test Email Verification

**1. Get Verification Code**

Check email for verification code (6 digits)

**2. Confirm User**

```bash
# Confirm user with code
aws cognito-idp confirm-sign-up \
  --client-id $CLIENT_ID \
  --username testuser \
  --confirmation-code 123456
```

**3. Verify Post-Confirmation Trigger**

```bash
# Check if user profile was created in DynamoDB
aws dynamodb get-item \
  --table-name EveryoneCook-dev \
  --key '{"PK":{"S":"USER#testuser"},"SK":{"S":"PROFILE"}}'

# Should return user profile with:
# - PK: USER#testuser
# - SK: PROFILE
# - userId: cognito-sub-id
# - email: test@example.com
# - fullName: Test User
# - birthday: null
# - gender: null
# - country: null
```

![User Profile Created](/images/5-Workshop/5.8-test-endpoints/user-profile-created.png)
*Screenshot: DynamoDB showing user profile created by trigger*

#### Step 5: Test User Login

**1. Sign In**

```bash
# Sign in to get tokens
TOKENS=$(aws cognito-idp initiate-auth \
  --client-id $CLIENT_ID \
  --auth-flow USER_PASSWORD_AUTH \
  --auth-parameters USERNAME=testuser,PASSWORD=TestPassword123!)

# Extract tokens
ACCESS_TOKEN=$(echo $TOKENS | jq -r '.AuthenticationResult.AccessToken')
ID_TOKEN=$(echo $TOKENS | jq -r '.AuthenticationResult.IdToken')
REFRESH_TOKEN=$(echo $TOKENS | jq -r '.AuthenticationResult.RefreshToken')

echo "ID Token: $ID_TOKEN"
```

**2. Verify Post-Authentication Trigger**

```bash
# Check if lastLoginAt was updated
aws dynamodb get-item \
  --table-name EveryoneCook-dev \
  --key '{"PK":{"S":"USER#testuser"},"SK":{"S":"PROFILE"}}' \
  --projection-expression "lastLoginAt"
```

#### Step 6: Test Profile Management

**1. Get Profile**

```bash
# Get user profile
curl -X GET \
  -H "Authorization: Bearer $ID_TOKEN" \
  $API_ENDPOINT/auth/profile

# Expected: User profile data
```

**2. Update Profile**

```bash
# Update profile (onboarding)
curl -X PUT \
  -H "Authorization: Bearer $ID_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "birthday": "1990-01-01",
    "gender": "male",
    "country": "US"
  }' \
  $API_ENDPOINT/auth/profile

# Expected: Updated profile
```

**3. Verify Update in DynamoDB**

```bash
# Check updated profile
aws dynamodb get-item \
  --table-name EveryoneCook-dev \
  --key '{"PK":{"S":"USER#testuser"},"SK":{"S":"PROFILE"}}'

# Should show birthday, gender, country updated
```

![Profile Updated](/images/5-Workshop/5.8-test-endpoints/profile-updated.png)
*Screenshot: Postman showing profile update successful*

#### Step 7: Test Social Features

**1. Create Post**

```bash
# Create a post
POST_RESPONSE=$(curl -X POST \
  -H "Authorization: Bearer $ID_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "content": "My first post on EveryoneCook!",
    "visibility": "public"
  }' \
  $API_ENDPOINT/social/posts)

POST_ID=$(echo $POST_RESPONSE | jq -r '.postId')
echo "Created post: $POST_ID"
```

**2. Get Posts Feed**

```bash
# Get posts
curl -X GET \
  -H "Authorization: Bearer $ID_TOKEN" \
  $API_ENDPOINT/social/posts

# Expected: Array of posts including the one just created
```

**3. Like Post**

```bash
# Like the post
curl -X POST \
  -H "Authorization: Bearer $ID_TOKEN" \
  $API_ENDPOINT/social/posts/$POST_ID/like

# Expected: Success message
```

**4. Comment on Post**

```bash
# Add comment
curl -X POST \
  -H "Authorization: Bearer $ID_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"content":"Great post!"}' \
  $API_ENDPOINT/social/posts/$POST_ID/comment

# Expected: Comment created
```

![Social Features](/images/5-Workshop/5.8-test-endpoints/social-features.png)
*Screenshot: Postman showing post creation and interactions*

#### Step 8: Test Recipe Features

**1. Create Recipe**

```bash
# Create a recipe
RECIPE_RESPONSE=$(curl -X POST \
  -H "Authorization: Bearer $ID_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Pho Bo (Vietnamese Beef Noodle Soup)",
    "description": "Traditional Vietnamese beef noodle soup",
    "ingredients": [
      {"name": "beef bones", "amount": "2", "unit": "kg"},
      {"name": "rice noodles", "amount": "500", "unit": "g"},
      {"name": "ginger", "amount": "1", "unit": "piece"}
    ],
    "instructions": [
      "Boil beef bones for 2 hours",
      "Add spices and simmer",
      "Prepare noodles and serve"
    ],
    "cuisine": "Vietnamese",
    "difficulty": "medium",
    "prepTime": 30,
    "cookTime": 120
  }' \
  $API_ENDPOINT/recipes)

RECIPE_ID=$(echo $RECIPE_RESPONSE | jq -r '.recipeId')
echo "Created recipe: $RECIPE_ID"
```

**2. Get Recipes**

```bash
# Get all recipes
curl -X GET \
  -H "Authorization: Bearer $ID_TOKEN" \
  $API_ENDPOINT/recipes

# Expected: Array of recipes
```

**3. Get Recipe by ID**

```bash
# Get specific recipe
curl -X GET \
  -H "Authorization: Bearer $ID_TOKEN" \
  $API_ENDPOINT/recipes/$RECIPE_ID

# Expected: Recipe details
```

#### Step 9: Test AI Features

**1. Generate Recipe with AI**

```bash
# Generate recipe using Bedrock
curl -X POST \
  -H "Authorization: Bearer $ID_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "ingredients": ["chicken", "rice", "vegetables"],
    "cuisine": "Vietnamese",
    "dietaryRestrictions": ["gluten-free"],
    "servings": 4
  }' \
  $API_ENDPOINT/ai/generate-recipe

# Expected: AI-generated recipe (takes 5-10 seconds)
# Response includes Vietnamese ingredient names
```

![AI Recipe Generation](/images/5-Workshop/5.8-test-endpoints/ai-recipe.png)
*Screenshot: Postman showing AI-generated recipe with Vietnamese names*

**2. Translate Ingredient**

```bash
# Translate ingredient to Vietnamese
curl -X POST \
  -H "Authorization: Bearer $ID_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "ingredient": "tomato",
    "targetLanguage": "vi"
  }' \
  $API_ENDPOINT/ai/translate

# Expected: {"translation": "cà chua", "confidence": 0.99}
```

#### Step 10: Test File Upload

**1. Request Pre-signed URL**

```bash
# Get pre-signed URL for avatar upload
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

**2. Upload File to S3**

```bash
# Create test image
echo "Test image content" > test-avatar.jpg

# Upload using pre-signed URL
curl -X PUT \
  -H "Content-Type: image/jpeg" \
  --upload-file test-avatar.jpg \
  "$PRESIGNED_URL"

# Expected: 200 OK
```

**3. Mark Upload Complete**

```bash
# Notify backend that upload is complete
curl -X POST \
  -H "Authorization: Bearer $ID_TOKEN" \
  -H "Content-Type: application/json" \
  -d "{\"key\":\"$UPLOAD_KEY\"}" \
  $API_ENDPOINT/upload/complete

# Expected: Success message
```

**4. Access via CloudFront**

```bash
# Access file via CloudFront CDN
curl -I https://cdn.everyonecook.cloud/$UPLOAD_KEY

# First request: X-Cache: Miss from cloudfront
# Second request: X-Cache: Hit from cloudfront
```

![File Upload](/images/5-Workshop/5.8-test-endpoints/file-upload.png)
*Screenshot: CloudFront cache headers showing Miss then Hit*

#### Step 11: Test Search (OpenSearch)

**If OpenSearch is enabled:**

```bash
# Search recipes with Vietnamese query
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

# Expected: Array of matching recipes
# Vietnamese analyzer handles: "phở bò" = "pho bo" = "beef noodle soup"
```

#### Step 12: Test Admin Features

**1. List Users (Admin Only)**

```bash
# Get all users (requires admin role)
curl -X GET \
  -H "Authorization: Bearer $ID_TOKEN" \
  $API_ENDPOINT/admin/users

# Expected: Array of users or 403 Forbidden if not admin
```

#### Step 13: Verify Async Processing

**1. Check SQS Queue Processing**

```bash
# Send message to SearchIndex queue
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

# Check worker logs
aws logs tail /aws/lambda/EveryoneCook-dev-SearchSyncWorker --follow
```

#### Testing Checklist

- [ ] Health check responds
- [ ] User registration works
- [ ] Email verification works
- [ ] User login successful
- [ ] Profile CRUD operations work
- [ ] Posts can be created
- [ ] Posts can be liked
- [ ] Comments can be added
- [ ] Recipes can be created
- [ ] AI recipe generation works
- [ ] Ingredient translation works
- [ ] File upload to S3 works
- [ ] CloudFront serves files
- [ ] Search works (if OpenSearch enabled)
- [ ] SQS queues process messages
- [ ] All Lambda functions execute without errors

#### Performance Benchmarks

**Expected Response Times:**
- Health check: < 50ms
- User login: < 200ms
- Get profile: < 100ms
- Create post: < 300ms
- Get posts feed: < 500ms
- AI recipe generation: 5-10 seconds
- File upload (pre-signed URL): < 100ms
- Search query: < 200ms

#### Next Steps

Once all tests pass, proceed to [Push to GitLab](../5.9-push-gitlab/) to version control your code and set up CI/CD.
