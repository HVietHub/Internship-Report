---
title: "Event 5"
date: 2025-11-29
weight: 5
chapter: false
pre: " <b> 4.5. </b> "
---

# AWS Well-Architected Security Pillar Workshop

### Event Information

**Date:** Friday, November 29, 2025  
**Time:** 8:30 AM – 12:00 PM (Morning Only)  
**Location:** AWS Vietnam Office, Bitexco Financial Tower, 2 Hai Trieu St., District 1, HCMC  
**Speakers:** Tran Toan Cong Ly, Hoang Anh, Tran Duc Anh, Nguyen Tuan Thinh, Nguyen Do Thanh Dat, Hoang Kha, Thinh Lam, Viet Nguyen, Mende Grabski (Long), Tinh Truong  
**Role:** Attendee

---

## Event Overview

Comprehensive workshop on AWS Well-Architected Security Pillar, covering the five fundamental pillars of cloud security with practical demonstrations and real-world scenarios from Vietnamese enterprises.

### Session Schedule

**8:30 – 8:50 AM | Opening & Security Foundation**
*Speakers: Tran Toan Cong Ly*

- **Security Pillar Role in Well-Architected Framework:**
  - Core principles: Least Privilege, Zero Trust, Defense in Depth
  - AWS Shared Responsibility Model
  - Top security threats in Vietnam's cloud environment

---

**⭐ Pillar 1 — Identity & Access Management**

**8:50 – 9:30 AM | Modern IAM Architecture**
*Speakers: Hoang Anh*

- **IAM Fundamentals:**
  - Users, Roles, Policies – avoiding long-term credentials
  - IAM Identity Center: SSO and permission sets
  - Service Control Policies (SCP) & permission boundaries for multi-account
  - MFA, credential rotation, Access Analyzer

- **Mini Demo:**
  - Validate IAM Policy
  - Simulate access scenarios

---

**⭐ Pillar 2 — Detection**

**9:30 – 9:55 AM | Detection & Continuous Monitoring**
*Speakers: Tran Duc Anh, Nguyen Tuan Thinh, Nguyen Do Thanh Dat*

- **Monitoring Services:**
  - CloudTrail (organization-level logging)
  - GuardDuty (threat detection)
  - Security Hub (centralized security view)

- **Comprehensive Logging:**
  - VPC Flow Logs
  - ALB/S3 access logs
  - Alerting & automation with EventBridge
  - Detection-as-Code (infrastructure + rules)

**9:55 – 10:10 AM | Coffee Break**

---

**⭐ Pillar 3 — Infrastructure Protection**

**10:10 – 10:40 AM | Network & Workload Security**
*Speakers: Hoang Kha*

- **Network Security:**
  - VPC segmentation strategies
  - Private vs public subnet placement
  - Security Groups vs NACLs: application models

- **Protection Services:**
  - AWS WAF (Web Application Firewall)
  - AWS Shield (DDoS protection)
  - AWS Network Firewall

- **Workload Protection:**
  - EC2 security basics
  - ECS/EKS container security

---

**⭐ Pillar 4 — Data Protection**

**10:40 – 11:10 AM | Encryption, Keys & Secrets**
*Speakers: Thinh Lam, Viet Nguyen*

- **Key Management:**
  - AWS KMS: key policies, grants, rotation
  - Encryption at-rest: S3, EBS, RDS, DynamoDB
  - Encryption in-transit: TLS/SSL

- **Secrets Management:**
  - AWS Secrets Manager
  - Systems Manager Parameter Store
  - Rotation patterns and best practices

- **Data Governance:**
  - Data classification strategies
  - Access guardrails and policies

---

**⭐ Pillar 5 — Incident Response**

**11:10 – 11:40 AM | IR Playbook & Automation**
*Speakers: Mende Grabski (Long), Tinh Truong*

- **Incident Response Lifecycle:**
  - Preparation
  - Detection & Analysis
  - Containment
  - Eradication & Recovery
  - Post-Incident Activity

- **IR Playbooks:**
  - Compromised IAM key response
  - S3 public exposure remediation
  - EC2 malware detection handling

- **Automation:**
  - Snapshot and evidence collection
  - Instance isolation procedures
  - Auto-response with Lambda/Step Functions

---

**11:40 – 12:00 PM | Wrap-Up & Q&A**

- **Summary of 5 Security Pillars**
- **Common Pitfalls:**
  - Real-world challenges in Vietnamese enterprises
  - Lessons learned from security incidents

- **Security Learning Roadmap:**
  - AWS Certified Security – Specialty
  - AWS Certified Solutions Architect – Professional
  - Continuous learning resources

---

## Key Takeaways

**Security Foundation:**
- Security is a shared responsibility between AWS and customers
- Implement defense in depth with multiple security layers
- Zero Trust approach: verify explicitly, use least privilege, assume breach

**IAM Best Practices:**
- Eliminate long-term credentials, use temporary credentials
- Implement MFA for all human users
- Use IAM roles for applications and services
- Regular access reviews with Access Analyzer

**Detection & Monitoring:**
- Enable CloudTrail at organization level
- Implement GuardDuty for threat detection
- Centralize security findings with Security Hub
- Automate responses to security events

**Infrastructure Protection:**
- Design VPC with proper segmentation
- Use Security Groups as primary firewall
- Implement WAF for web applications
- Protect workloads with appropriate security controls

**Data Protection:**
- Encrypt data at rest and in transit
- Use KMS for centralized key management
- Rotate secrets regularly with Secrets Manager
- Classify data and apply appropriate controls

**Incident Response:**
- Prepare IR playbooks before incidents occur
- Automate response actions where possible
- Practice incident response procedures
- Learn from incidents and improve

**Vietnamese Enterprise Context:**
- Common security challenges in local market
- Compliance requirements (PDPA, local regulations)
- Cost-effective security implementation strategies

---

## Event Photos

![AWS Well-Architected Security Pillar Workshop](/images/4-EvenPartcipated/event-5.jpg)

---

**Rating:** ⭐⭐⭐⭐⭐ (5/5)

> Comprehensive and practical security workshop covering all essential aspects of AWS security with real-world scenarios relevant to Vietnamese enterprises.

