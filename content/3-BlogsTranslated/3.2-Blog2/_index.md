---
title: "Blog 2"
date: 2025-09-09
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

# 6 Lessons Game Developers Can Learn from Epic Games' Cloud Governance Strategy

By Gena Gizzi and Ryan Rumble, on 03/12/2025 in Amazon EC2, Amazon EBS, Amazon ElastiCache, Amazon EventBridge, Amazon RDS, Application Integration, Application Services, AWS Lambda, AWS Step Functions, Compute, Customer Solutions, Database, Game Development, Games, Graviton, Industries, Storage

*This post is co-authored by Reza Nikoopour, Principal Engineer, Cloud Governance at Epic Games.*

---

For game studios large and small, a smart cloud governance strategy can play a crucial role in reducing costs and enhancing security for a live service game. Here are some key lessons from Epic Games' experience working with Amazon Web Services (AWS) that game developers should consider when building their cloud governance strategy.

Since Fortnite launched in 2017, Epic's player base has reached 500 million registered accounts. Fortnite is built on AWS, using services like Amazon EC2 for backend infrastructure. To this day, Epic continues to rely on AWS technology to operate its global server fleet.

With AWS, Epic can automatically scale compute capacity based on player demand fluctuations as the game continues to grow.

> "AWS has been instrumental in guiding us as we established our cloud governance team at Epic. As a result, we've reduced cloud costs by approximately 20% while improving resource observability across the entire system."  
> — Shane Smith, Vice President of Technology Services at Epic Games

This was achieved in part through an initiative launched in 2022 aimed at achieving cloud efficiency, not just proficiency, while transforming Epic's backend to support Fortnite's continued growth.

---

## 1. Establish Guardrails

Game developers often dream of releasing a blockbuster, but predicting popularity is very difficult. Strategies like tagging, access management, and cloud resource governance are often not prioritized during rapid growth phases. When a game unexpectedly becomes a phenomenon, the focus typically shifts entirely to maintaining player experience.

However, cloud governance is vital because it provides a set of rules, processes, and reports that guide best practices. Ensuring comprehensive visibility of resources and understanding critical game pathways will help developers establish a stronger position in the long term.

---

## 2. Leverage AWS Expertise

To improve cloud governance, Epic established a steering committee consisting of internal technical leaders and representatives from the AWS account team. Initial discussions focused on cloud maturity levels, best practices, and how AWS could help Epic operate more efficiently.

From there, Epic worked closely with AWS Professional Services and AWS Customer Solutions Manager. They assembled a diverse group of cloud-passionate engineers from various departments to form the Cloud Governance team, also known as the cloud center of excellence (CCoE).

---

## 3. Understand Ownership

To implement meaningful change, Epic needed a standardized approach to identify AWS account ownership. The Cloud Governance team identified the person responsible for each account and created real-time communication channels for quick information exchange.

This foundation allowed Epic to build and enforce multiple additional controls across the entire cloud environment.

---

## 4. Provision Access

The Cloud Governance team focused on providing self-service access to AWS, using a combination of GitHub, Okta, SailPoint, and AWS IAM Identity Center.

- **GitHub**: stores infrastructure as code (IaC) defining IAM roles and deployment locations.
- **AWS IAM Identity Center**: distributes standard IAM roles across all Epic AWS accounts.
- **Okta**: provides identities and permission groups for access control.
- **SailPoint**: allows account managers to approve or deny access requests.

---

## 5. Automate Configuration

After implementing self-service access, Epic uses AWS Step Functions to deploy baseline configuration for each account.

This includes:

- Default components like IAM roles
- Cost-saving measures, such as log expiration for AWS Lambda
- Security measures, such as enabling Amazon EBS encryption by default in all regions

With AWS Step Functions and AWS Lambda, Epic can focus on core configuration without worrying about workflow orchestration, while ensuring all accounts are set up to standard.

---

## 6. Enforce Standards

Before deploying policies, Epic needed appropriate mechanisms, so they partnered with governance solution providers.

Epic forwards events from all accounts to a single point through Amazon EventBridge for analysis and processing. This allows the Cloud Governance team to enforce a minimum tag set to provide information about ownership and costs.

Automated mechanisms immediately remove any resources without required tags. Similarly, Epic also applies mechanisms requiring Amazon RDS and Amazon ElastiCache to run on AWS Graviton for cost optimization, but allows exceptions when requested.

Additionally, Epic introduced mandatory scanning of infrastructure as code (IaC) changes when pull requests are made in GitHub. If issues are detected, the system automatically comments on the pull request, helping developers fix errors before deployment.

---

## Conclusion

By reviewing people, processes, and technology, Epic has made significant progress in their cloud governance journey and achieved clear results.

Beyond cost savings, Epic now has better resource observability, with nearly 100% of AWS resources tagged in both production and development environments. Continuous access auditing and centralized monitoring have also enhanced security while promoting a positive culture throughout the company.

For game developers who want to learn more about smart cloud governance strategies, explore how other companies implement best practices and contact your AWS Representative for support.

---

## Authors
![Juho Jantunen](/images/3-BlogsTranslated/blog2-author-1.png)
**Gena Gizzi:**  
Gena Gizzi is a Senior Game Solutions Architect at AWS. She helps customers develop, launch, and scale their games and businesses on AWS.
![Juho Jantunen](/images/3-BlogsTranslated/blog2-author-2.png)
**Ryan Rumble:**  
Ryan Rumble is the Head of Customer Solutions at AWS. In addition to driving Program Management for the team and customers, Ryan specializes in helping companies navigate transformation and maturity on their cloud journey.

