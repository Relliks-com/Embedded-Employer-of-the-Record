
---

# C4 Level 2: Container Diagram

## Container Diagram - Acme Platform Architecture
```text
┌───────────────────────────────────────────────────────────────────────────────────┐
│                              ACME PLATFORM                                        │
│                         Container Architecture                                    │
└───────────────────────────────────────────────────────────────────────────────────┘

                                  Internet
                                     │
                                     │ HTTPS (TLS 1.3)
                                     ▼
                    ┌────────────────────────────────────┐
                    │         API Gateway                │
                    │     (Kong / AWS API Gateway)       │
                    │  • Authentication                  │
                    │  • Rate limiting (1000 req/min)    │
                    │  • Request logging                 │
                    │  • SSL termination                 │
                    └──────────┬─────────────────────────┘
                               │
                               │ Internal HTTP
               ┌───────────────┼───────────────┐
               │               │               │
               ▼               ▼               ▼
    ┌──────────────────┐ ┌──────────────┐ ┌─────────────────┐
    │  Web Frontend    │ │  Mobile App  │ │  Admin Portal   │
    │  (React SPA)     │ │  (Native)    │ │  (React)        │
    │  • Onboarding UI │ │  • Workflows │ │  • Reconcile    │
    │  • Status dashbd │ │  • Push nots │ │  • Audit logs   │
    │  • Real-time     │ │              │ │  • Analytics    │
    └──────────────────┘ └──────────────┘ └─────────────────┘
               │               │               │
               └───────────────┼───────────────┘
                               │
                               ▼
              ┌────────────────────────────────────────────┐
              │       Integration Service                  │
              │     (Node.js / Python FastAPI)             │
              │  • Orchestrate Remote API calls            │
              │  • Distributed transactions (Saga)         │
              │  • Retry logic & circuit breaker           │
              │  • Idempotency handling                    │
              │  • Bi-directional sync                     │
              │  Endpoints:                                │
              │  • POST /api/v1/employments                │
              │  • GET /api/v1/employments/{id}            │
              │  • PATCH /api/v1/employments/{id}          │
              │  • POST /api/v1/employments/{id}/reconcile │
              └──────┬─────────────────┬───────────────────┘
                     │                 │
           ┌─────────┴────┐      ┌─────┴──────────┐
           ▼              ▼      ▼                ▼
    ┌─────────────┐ ┌──────────────────┐  ┌─────────────────┐
    │ PostgreSQL  │ │  Redis Cache     │  │  S3 Storage     │
    │ Database    │ │  (ElastiCache)   │  │  • Documents    │
    │ • employmnts│ │  • Employment    │  │  • Audit logs   │
    │ • processed_│ │    status (5m)   │  │  • Exports      │
    │   events    │ │  • OAuth tokens  │  │                 │
    │ • sync_state│ │    (TTL: 3600s)  │  │  Versioning ON  │
    │ • audit_logs│ │  • Session data  │  │                 │
    └─────────────┘ └──────────────────┘  └─────────────────┘

```



