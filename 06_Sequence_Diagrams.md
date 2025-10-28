# Sequence Diagrams

## 1. Employment Creation - Happy Path
```text
┌─────────┐  ┌──────────┐  ┌─────────────┐  ┌───────────┐  ┌──────────┐
│  User   │  │  Acme UI │  │Integration  │  │  Acme DB  │  │  Remote  │
└────┬────┘  └────┬─────┘  └──────┬──────┘  └─────┬─────┘  └────┬─────┘
     │            │                │                │             │
     │ "Hire Globally"             │                │             │
     ├───────────>│                │                │             │
     │            │ POST /api/v1/employments        │             │
     │            ├───────────────>│                │             │
     │            │                │ Create local record          │
     │            │                │ POST /v1/employments → Remote│
     │            │                ├─────────────────────────────>│
     │            │                │                │ 201 Created │
     │            │                │<─────────────────────────────┤
     │            │ 201 Created ←──┤                │             │
     │<───────────┤                │                │             │
     │            │ Show onboarding link            │             │
```

2. Failure Scenario - Remote API Timeout with Retry

```
┌─────────────┐  ┌──────────┐  ┌───────────┐
│Integration  │  │  Acme DB │  │  Remote   │
└──────┬──────┘  └────┬─────┘  └─────┬─────┘
       │ INSERT employment (PENDING_REMOTE_CREATE)
       ├─────────────>│
       │ POST /v1/employments (Timeout 30s)
       ├──────────────────────────────>│
       │<──────────────────────────────X
       │ Retry with exponential backoff (1s → 4s)
       ├──────────────────────────────>│
       │<──────────────────────────────┤ 201 Created
       │ UPDATE employment status='PENDING_EMPLOYEE_DETAILS'
       ├─────────────>│
       │ Saga COMPLETED
```

3. Idempotency Protection - Duplicate Webhook
```
┌──────────┐  ┌─────────────┐  ┌──────────┐  ┌─────────────┐
│  Remote  │  │  Webhook    │  │  Event   │  │   Acme DB   │
│  System  │  │  Receiver   │  │ Processor│  │             │
└────┬─────┘  └──────┬──────┘  └────┬─────┘  └──────┬──────┘
     │ Webhook event_id="evt_123"
     ├──────────────>│
     │               │ Validate HMAC ✓
     │               │ Publish to SQS
     │               │ Check idempotency in processed_events
     │               ├──────────────>│
     │               │ NOT FOUND → Process event
     │               │ UPDATE employment SET status='VERIFIED'
     │               │ Mark processed (INSERT processed_events)
     │ Duplicate webhook arrives
     │               │ FOUND! → Skip processing
     │               │ Ack queue message
```

