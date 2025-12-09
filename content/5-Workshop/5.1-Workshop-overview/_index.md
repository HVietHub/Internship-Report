---
title: "Workshop Overview"
date: 2025-12-01
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

#### EveryoneCook Architecture

**EveryoneCook** is a modern social cooking platform built entirely on AWS serverless technologies. The architecture follows best practices for scalability, security, and cost optimization, with a focus on Vietnamese ingredient support and AI-powered recipe suggestions.

#### Key Components

+ **Frontend**: Next.js 15 application with Flowbite React components hosted on AWS Amplify
+ **Backend**: Serverless API using API Gateway with API Router pattern and 6 Lambda modules
+ **Database**: DynamoDB Single Table Design with username-based PK and 5 GSI indexes
+ **Search**: OpenSearch with Vietnamese analyzer for advanced recipe and post search
+ **Storage**: S3 buckets with Intelligent-Tiering and CloudFront CDN with OAC
+ **Authentication**: Cognito User Pool with Lambda triggers for custom flows
+ **Email**: SES with DKIM signing for transactional emails
+ **AI Integration**: Amazon Bedrock (Claude 3.5 Sonnet v2) with dictionary-first translation
+ **Security**: WAF for API Gateway, Shield Standard for CloudFront, KMS encryption
+ **Monitoring**: CloudWatch dashboards, alarms, and X-Ray distributed tracing

#### Architecture Diagram

```
                    ┌─────────────────────────────────┐
                    │      Route 53 DNS               │
                    │   everyonecook.cloud            │
                    └──────┬──────────────┬───────────┘
                           │              │
                ┌──────────▼──────┐  ┌───▼──────────────┐
                │  CloudFront CDN │  │ Amplify Hosting  │
                │  cdn.*          │  │ everyonecook.*   │
                │  (Shield Std)   │  │ Next.js 15       │
                └──────┬──────────┘  └──────────────────┘
                       │ OAC
                ┌──────▼──────────────────────────────┐
                │      S3 Buckets (4)                 │
                │  - Content (avatars, posts)         │
                │  - Logs (CloudWatch archive)        │
                │  - Emails (SES incoming)            │
                │  - CDN Logs (CloudFront)            │
                │  Intelligent-Tiering + KMS          │
                └─────────────────────────────────────┘

┌────────────────────────────────────────────────────────────────┐
│                    API Gateway (api.*)                          │
│              Cognito Authorizer + WAF                           │
└──────────────────────────┬─────────────────────────────────────┘
                           │
                  ┌────────▼────────┐
                  │   API Router    │
                  │   Lambda        │
                  └────────┬────────┘
                           │
        ┌──────────────────┼──────────────────┐
        │                  │                  │
   ┌────▼────┐      ┌─────▼─────┐     ┌─────▼─────┐
   │  Auth   │      │  Social   │     │  Recipe   │
   │  Module │      │  Module   │     │  + AI     │
   └────┬────┘      └─────┬─────┘     └─────┬─────┘
        │                  │                  │
   ┌────▼────┐      ┌─────▼─────┐           │
   │  Admin  │      │  Upload   │           │
   │  Module │      │  Module   │           │
   └────┬────┘      └─────┬─────┘           │
        │                  │                  │
        └──────────────────┼──────────────────┘
                           │
        ┌──────────────────┼──────────────────┐
        │                  │                  │
   ┌────▼────────┐   ┌────▼────────┐   ┌────▼────────┐
   │  DynamoDB   │   │  OpenSearch │   │  Bedrock AI │
   │  Single     │   │  Vietnamese │   │  Claude 3.5 │
   │  Table      │   │  Analyzer   │   │  Sonnet v2  │
   └─────────────┘   └─────────────┘   └─────────────┘

┌────────────────────────────────────────────────────────────────┐
│                    SQS Queues (6)                               │
│  AI | Email | SearchIndex | Image | Analytics | Notification   │
└──────────────────────────┬─────────────────────────────────────┘
                           │
                  ┌────────▼────────┐
                  │  Search Sync    │
                  │  Worker Lambda  │
                  └─────────────────┘

┌────────────────────────────────────────────────────────────────┐
│              Cognito User Pool + 5 Lambda Triggers              │
│  Post-Confirmation | Pre-Auth | Post-Auth | Custom-Msg | Pre-Signup │
└────────────────────────────┬───────────────────────────────────┘
                             │
                    ┌────────▼────────┐
                    │   SES Email     │
                    │   DKIM + SPF    │
                    └─────────────────┘

┌────────────────────────────────────────────────────────────────┐
│         CloudWatch Dashboards + Alarms + X-Ray Tracing          │
└────────────────────────────────────────────────────────────────┘
```

#### Workshop Flow

This workshop follows a practical application development workflow:

1. **Setup Environment** - Install tools (Node.js, AWS CLI, CDK CLI)
2. **CDK Bootstrap** - Prepare AWS account for CDK deployments
3. **Configure Stacks** - Set up infrastructure configuration (DNS, Certificate, Core, Auth, Backend, Observability)
4. **Deploy Infrastructure** - Deploy all CDK stacks to AWS
5. **Configure API & Lambda** - Set up API Gateway routes and Lambda functions
6. **Deploy Backend** - Deploy API and Lambda code
7. **Test Endpoints** - Verify all endpoints work end-to-end
8. **Push to GitLab** - Version control and CI/CD setup
9. **Deploy to Amplify** - Deploy frontend to AWS Amplify
10. **Monitor & Maintain** - Use CloudWatch and X-Ray for monitoring

#### What You'll Learn

+ Infrastructure as Code with AWS CDK (TypeScript)
+ Serverless architecture with API Router pattern
+ DynamoDB Single Table Design with username-based PK
+ OpenSearch with Vietnamese language analyzer
+ CloudFront CDN with Origin Access Control (OAC)
+ Cognito authentication with Lambda triggers
+ Lambda function modular organization (6 modules)
+ SQS-based async processing with workers
+ Bedrock AI integration with caching strategy
+ WAF security for API Gateway
+ CloudWatch monitoring and X-Ray tracing
+ Cost optimization strategies ($15-24/day for 10K users)

#### Key Features

+ **Vietnamese Support**: OpenSearch Vietnamese analyzer for ingredient search
+ **AI-Powered**: Bedrock Claude 3.5 Sonnet v2 for recipe generation
+ **Dictionary-First Translation**: 99% coverage target with intelligent caching
+ **Social Platform**: Posts, comments, reactions, friends, notifications
+ **Field-Level Privacy**: Granular control over profile visibility
+ **Content Moderation**: Automated and manual moderation workflows
+ **Advanced Search**: Full-text search with Vietnamese normalization
+ **Cost Optimized**: Intelligent-Tiering, caching, and resource optimization
