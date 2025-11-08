---
title: "Proposal"
date: 2025-09-09
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Mini Food Social  
## A Serverless Social Platform with AI-Powered Recipe Generation

### 1. Executive Summary
**Mini Food Social** is a fully serverless, AI-enhanced social platform built for food enthusiasts.  
It enables users to share posts, upload food images, and generate new recipe ideas through AI.  
The system supports up to **100 registered users**, providing authentication, CRUD functionality, and interactive features such as likes and comments.

The architecture leverages **AWS managed services** for scalability, cost efficiency, and minimal operational overhead:
- **AWS Amplify** (Next.js frontend hosting & CI/CD)
- **Amazon API Gateway** + **AWS Lambda** (backend APIs)
- **Amazon DynamoDB** (NoSQL data store)
- **Amazon Bedrock** (AI text generation)
- **Amazon Cognito** (authentication)
- **Amazon CloudFront + WAF + Route 53** (secure content delivery)

This solution provides an affordable, production-ready foundation for modern serverless applications.

---

### 2. Problem Statement
#### The Problem
Traditional recipe-sharing apps lack personalization and dynamic engagement.  
Users increasingly seek:
- Interactive communities (posting, liking, commenting)
- Smart recipe generation using AI  
- Reliable authentication and security  

However, such platforms often demand heavy backend infrastructure and high costs.

#### The Solution
**Mini Food Social** employs a fully **serverless** and **event-driven** AWS stack:
- **Amplify** hosts and deploys the frontend via GitLab CI/CD.  
- **Cognito** manages secure sign-in/sign-up and JWT tokens.  
- **API Gateway** connects requests to Lambda functions.  
- **Lambda Router** delegates tasks to handlers for posts, profiles, and AI requests.  
- **Bedrock** generates personalized recipes using user prompts.  
- **CloudFront + WAF** ensure fast and secure traffic delivery.  

---

### Benefits and Return on Investment
- **Low operational cost** — Pay only for actual usage.  
- **Highly scalable** — Auto-scales with user demand.  
- **AI-powered engagement** — Improves user retention and creativity.  
- **Secure and compliant** — Cognito authentication and encrypted data in DynamoDB & S3.  

**Estimated Monthly Cost (No Free Tier): ≈ $16.70 USD**  
**Annual Cost: ≈ $200.40 USD**

---

### 3. Solution Architecture
The system follows a **five-layer serverless architecture** that ensures modularity, scalability, and cost control.

At the **CI/CD Layer**, GitLab automates deployments — pushing frontend code to **AWS Amplify** and provisioning backend infrastructure via **AWS CDK** and **CloudFormation**.

The **Edge Layer** secures and routes all requests using **Amazon Route 53** for DNS, **WAF** for traffic filtering, and **CloudFront** for global content caching.  
SSL/TLS certificates are automatically managed by **AWS Amplify** (no separate ACM required).  
CloudFront communicates privately with **S3** using an **Origin Access Identity (OAI)**.

In the **Application Layer**, Amplify hosts the **Next.js frontend**, while **API Gateway** and **Lambda** handle backend logic. **Cognito** authenticates users and manages tokens for API access.

The **Data Storage Layer** uses **DynamoDB** for core data, **S3** for image storage.

Finally, the **Observability Layer** integrates **CloudWatch** (logs & metrics), **X-Ray** (request tracing), and **SNS** (alerting), ensuring transparency and reliability.

![Mini Food Social Architecture](/images/2-Proposal/FsocialArchitecture.png)

### AWS Services Used
| Category | Services |
|-----------|-----------|
| **Frontend & CI/CD** | AWS Amplify (Next.js), GitLab, AWS CDK, CloudFormation |
| **Edge & Security** | Route 53, CloudFront, WAF |
| **Application & Compute** | API Gateway, AWS Lambda, Amazon Cognito |
| **Data & AI** | DynamoDB (single-table), S3, Amazon Bedrock |
| **Observability & Events** | CloudWatch, X-Ray, SNS |

---

### Component Design
- **User Access:** User → CloudFront → Amplify (Frontend)  
- **Authentication:** Amplify → Cognito (Hosted UI redirect)  
- **API Calls:** Amplify → Route 53 → CloudFront → WAF → API Gateway  
- **API Authorization:** API Gateway → Cognito Authorizer  
- **Backend Logic:** API Router Lambda → DynamoDB / S3 / Bedrock  
- **AI Recipes:** AI Handler Lambda → Bedrock (Claude model)  
- **Image Delivery:** CloudFront → S3 (via OAI)  
- **Events & Logs:** CloudWatch, X-Ray, SNS  

---

### 4. Technical Implementation
#### Implementation Phases
| Phase | Duration | Description |
|--------|-----------|-------------|
| **1. Setup & CI/CD** | Weeks 1–3 | CDK setup, Amplify integration, GitLab pipelines |
| **2. Core Backend** | Weeks 4–7 | Cognito auth, API Gateway, Lambda routing |
| **3. Frontend Integration** | Weeks 8–10 | Next.js UI + API connections |
| **4. AI & Monitoring** | Weeks 11–13 | Bedrock integration, CloudWatch & X-Ray setup |
| **5. Final Testing** | Week 14 | Load testing, production deployment |

#### Technical Requirements
- **Frontend:** Next.js (Amplify Hosting)  
- **Backend:** Node.js (Lambda), API Gateway, Cognito  
- **AI:** Amazon Bedrock (Claude model)  
- **Database:** DynamoDB (single-table, GSI)  
- **DevOps:** GitLab CI/CD for Amplify & CDK  

---

### 5. Timeline & Milestones
| Month | Deliverables |
|--------|--------------|
| **Month 1** | Architecture finalized, CI/CD ready |
| **Month 2** | Authentication & backend APIs complete |
| **Month 3** | Frontend, AI, and observability layers live |
| **End of Month 3** | Production-ready release |

---

### 6. Budget Estimation
#### AWS Service Cost Estimation (100 active users, no free tier)
| Service | Description | Estimated Monthly Cost (USD) |
|----------|--------------|-------------------------------|
| **AWS Amplify** | Frontend hosting & CI/CD builds | **$2.00** |
| **Amazon CloudFront** | CDN delivery (100GB data) | **$5.00** |
| **AWS WAF** | 1 Web ACL with basic rules | **$5.00** |
| **Amazon API Gateway** | ~25,000 API requests/month | **$0.40** |
| **AWS Lambda** | 4 functions, ~1M invocations | **$0.80** |
| **Amazon DynamoDB** | On-demand mode (moderate access) | **$0.60** |
| **Amazon S3** | 5GB assets + 20GB images | **$0.50** |
| **Amazon Cognito** | 100 Monthly Active Users | **$0.50** |
| **Amazon Bedrock** | AI text generation (~50K tokens) | **$2.50** |
| **Amazon Route 53** | Domain + DNS + hosted zone | **$1.30** |
| **CloudFormation / SNS / X-Ray** | Infra mgmt + monitoring | **$0.00** |

**➡ Total Estimated Monthly Cost:** ≈ **$16.70 USD**  
**➡ Annual Cost:** ≈ **$200.40 USD**

---

### 7. Risk Assessment
| Risk | Impact | Probability |
|-------|---------|-------------|
| Unexpected Bedrock usage | Medium | Medium |
| Cognito misconfiguration | High | Medium |
| WAF rule blocking valid traffic | Medium | Low |

**Mitigation Strategies:**  
- Implement CloudWatch budget alarms for Bedrock usage.  
- Apply IAM least privilege for Lambda roles.  
- Test WAF rules in staging before production rollout.

---

### 8. Expected Outcomes
#### Technical Achievements
- Fully functional **serverless social platform** with AI-generated recipes.  
- Scalable and secure architecture using **Cognito**, **WAF**, and **CloudFront**.  
- Comprehensive observability with **CloudWatch** and **X-Ray**.

#### Long-term Benefits
- Demonstrates real-world expertise in **Serverless DevOps and AI integration**.  
- Supports up to **500+ users** with minor scaling adjustments.  
- Provides a reusable AWS reference architecture for future projects.