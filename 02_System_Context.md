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
