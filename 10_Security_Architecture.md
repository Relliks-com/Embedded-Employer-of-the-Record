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

Policies

ALB → ECS only (port 443)

ECS Tasks → DB/Redis (private network only)

No outbound internet access from ECS tasks except via NAT Gateway

Ingress filtered by AWS WAF

Security audit logs to CloudWatch and S3

Data Protection

Encryption in Transit: TLS 1.3 end-to-end

Encryption at Rest: AES-256 (S3, RDS, Redis)

Secrets: AWS Secrets Manager (auto-rotation 90 days)

PII: Encrypted columns using KMS CMKs per tenant

Key Rotation: automated and versioned through CI/CD

Application Security

CSRF and CORS hardened

JWT expiration: 15 min, refresh token TTL: 24 h

HMAC-signed webhooks (256-bit keys)

Input validation (OWASP ASVS L1 + L2)

Vulnerability scanning: Trivy + Dependabot

Static analysis: SonarQube gated on PRs

Incident Response

Alerts via PagerDuty → Security Team on critical findings

Forensics retention: 180 days (S3 Glacier)

Incident bridge auto-opens via Slack + Zoom

Compliance: SOC2, GDPR, ISO 27001 aligned
