# C4 Level 1: System Context

## System Context Diagram
```text
┌─────────────────────────────────────────────────────────────────────────────┐
│                         SYSTEM CONTEXT DIAGRAM                              │
│                     Remote EOR Embedded Integration                         │
└─────────────────────────────────────────────────────────────────────────────┘

                          ┌─────────────────┐
                          │                 │
                          │   Acme Users    │
                          │  (Customers)    │
                          │                 │
                          │ • HR Managers   │
                          │ • Recruiters    │
                          │ • Finance Teams │
                          │                 │
                          └────────┬────────┘
                                   │
                                   │ HTTPS/WebSocket
                                   │ "Hire employees globally"
                                   │ "View onboarding status"
                                   │ "Manage employee lifecycle"
                                   │
                                   ▼
        ┌────────────────────────────────────────────────────────────┐
        │                                                            │
        │                    ACME PLATFORM                           │
        │          (Global Hiring Management System)                 │
        │                                                            │
        │  Capabilities:                                             │
        │  • Employee onboarding workflows                           │
        │  • Real-time status tracking & notifications               │
        │  • Time-off management & approvals                         │
        │  • Document collection & storage                           │
        │  • Compliance reporting                                    │
        │  • Bi-directional sync with Remote EOR                     │
        │                                                            │
        └───────────┬──────────────────────────┬─────────────────────┘
                    │                          │
                    │ REST API                 │ Webhooks
                    │ (OAuth 2.0)              │ (HMAC-SHA256)
                    │ TLS 1.3                  │ Signed payloads
                    │                          │
                    ▼                          ▲
        ┌─────────────────────────────────────────────────────────────┐
        │                                                             │
        │              REMOTE EOR PLATFORM                            │
        │            (External System - Partner)                      │
        │                                                             │
        │  Capabilities:                                              │
        │  • Employment contract generation & management              │
        │  • Global compliance & payroll processing                   │
        │  • Multi-country employment portal                          │
        │  • Document verification & background checks                │
        │  • Benefits administration                                  │
        │  • Tax filing & reporting                                   │
        │                                                             │
        └──────────────────────┬──────────────────────────────────────┘
                               │
                               │ Direct HTTPS access
                               │ "Complete onboarding"
                               │ "Submit documents"
                               │ "Sign contracts"
                               │
                               ▼
                     ┌───────────────────┐
                     │                   │
                     │    Employees      │
                     │   (End Users)     │
                     │                   │
                     │ • New hires       │
                     │ • Contractors     │
                     │                   │
                     └───────────────────┘
```

Key Interactions

Acme Users → Acme Platform

Initiate employee onboarding

Monitor status in real-time

Approve time-off requests

View compliance reports

Acme Platform → Remote API

Create employment contracts (POST /v1/employments)

Fetch current status (GET /v1/employments/{id})

Update employee details (PATCH /v1/employments/{id})

List time-off requests (GET /v1/timeoff)

Remote Platform → Acme Platform

Send lifecycle webhooks (employment.verified, employment.active)

Push real-time updates (timeoff.approved, document.uploaded)

Notify about critical events (compliance issues, terminations)

Employees → Remote Platform

Complete onboarding forms

Upload identity documents

Sign employment contracts

Request time-off

External Dependencies

AWS Services

ECS Fargate (compute)

RDS PostgreSQL (database)

ElastiCache Redis (caching)

SQS (message queuing)

Secrets Manager (key storage)

Monitoring & Observability

DataDog (metrics & APM)

PagerDuty (alerting)

Sentry (error tracking)

Business Context
Primary Use Cases

Global Hiring: Acme customer hires employee in 150+ countries

Onboarding Orchestration: Track multi-step process across systems

Lifecycle Management: Sync employee changes bidirectionally

Compliance Assurance: Audit trail + reconciliation

Success Criteria

95% of employments created without manual intervention

<48 hours median time-to-active

99.9% system uptime

<0.1% data inconsistency rate

```
