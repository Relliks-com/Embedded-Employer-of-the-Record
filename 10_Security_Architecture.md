# Security Architecture

## Overview
Security is implemented through a **defense-in-depth** strategy, combining encryption, authentication, network isolation, and continuous monitoring.

---

## Identity & Access Management
| Layer | Mechanism | Description |
|-------|------------|-------------|
| Public API | OAuth 2.0 (Client Credentials & Authorization Code Flows) | All partner-initiated API requests are scoped by client_id |
| Internal Services | IAM Roles for Tasks | Fine-grained permissions for ECS tasks |
| Developers | SSO + MFA (Okta) | Centralized access management |

### Role Matrix
| Role | Access | Description |
|------|---------|-------------|
| `system_admin` | Full | Manage deployments, rotate secrets |
| `integration_service` | Read/Write limited to employment objects | Service role |
| `readonly_analyst` | SELECT only | Reporting purposes via Redshift mirror |
| `dev_user` | Non-prod only | Development sandbox |

---

## Network Security
```text
┌──────────────────────────────┐
│     PUBLIC SUBNET            │
│  • CloudFront CDN            │
│  • ALB                       │
└────────────┬─────────────────┘
             │
             ▼
┌──────────────────────────────┐
│     PRIVATE SUBNET A         │
│  • ECS Tasks (Integration)   │
│  • Redis Cluster Node A      │
└────────────┬─────────────────┘
             │
             ▼
┌──────────────────────────────┐
│     PRIVATE SUBNET B         │
│  • RDS Multi-AZ Replica      │
│  • Redis Node B              │
└──────────────────────────────┘
```

## 🔐 Security & Compliance Policies

### 🧱 Network & Access Policies

- **ALB → ECS only** (port **443**)  
- **ECS Tasks → DB/Redis** (private network only)  
- **No outbound internet access** from ECS tasks except via **NAT Gateway**  
- **Ingress** filtered by **AWS WAF**  
- **Security audit logs** streamed to **CloudWatch** and **S3**

### 🔒 Data Protection

| Control | Description |
|----------|-------------|
| **Encryption in Transit** | TLS 1.3 end-to-end |
| **Encryption at Rest** | AES-256 (S3, RDS, Redis) |
| **Secrets Management** | AWS Secrets Manager (auto-rotation every 90 days) |
| **PII Protection** | Encrypted columns using **KMS CMKs per tenant** |
| **Key Rotation** | Automated and versioned through CI/CD |

### 🛡️ Application Security

- **CSRF** and **CORS** hardened  
- **JWT** expiration: **15 minutes**, refresh token TTL: **24 hours**  
- **HMAC-signed webhooks** using **256-bit keys**  
- **Input validation** aligned with **OWASP ASVS L1 + L2**  
- **Vulnerability scanning:** Trivy + Dependabot  
- **Static analysis:** SonarQube (PR-gated checks) 

### 🚨 Incident Response

- **Alerts:** PagerDuty → Security Team (critical findings)  
- **Forensics retention:** 180 days (S3 Glacier)  
- **Incident bridge:** auto-opens via Slack + Zoom  
- **Compliance:** SOC 2, GDPR, ISO 27001 aligned  
