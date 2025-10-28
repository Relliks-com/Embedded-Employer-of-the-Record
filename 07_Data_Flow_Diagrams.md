# Data Flow Diagrams

## Level 0: High-Level Data Flow
```text
┌──────────────────────────────────────────────────────────────┐
│                     ACME USERS                               │
│   • HR Managers                                              │
│   • Recruiters                                               │
│   • Finance Teams                                            │
└───────────────┬──────────────────────────────────────────────┘
                │
                │ 1. Initiate hire
                ▼
┌──────────────────────────────────────────────────────────────┐
│                    ACME PLATFORM                             │
│   • Collect employment details                               │
│   • Validate local data                                      │
│   • Store draft record                                       │
└───────────────┬──────────────────────────────────────────────┘
                │
                │ 2. Send data → Integration Service
                ▼
┌──────────────────────────────────────────────────────────────┐
│                INTEGRATION SERVICE                            │
│   • Validate payload                                          │
│   • Transform fields to Remote schema                         │
│   • Invoke Remote API (create employment)                     │
│   • Persist mapping: local_id ↔ remote_id                     │
└───────────────┬──────────────────────────────────────────────┘
                │
                │ 3. Forward → Remote EOR Platform
                ▼
┌──────────────────────────────────────────────────────────────┐
│               REMOTE EOR PLATFORM                            │
│   • Create employment                                        │
│   • Generate contract                                        │
│   • Notify via webhook                                       │
└───────────────┬──────────────────────────────────────────────┘
                │
                │ 4. Webhook → Integration Service
                ▼
┌──────────────────────────────────────────────────────────────┐
│          WEBHOOK RECEIVER                                    │
│   • Verify HMAC signature                                    │
│   • Deduplicate events                                       │
│   • Publish to message queue                                 │
│   • Update employment state                                  │
└───────────────┬──────────────────────────────────────────────┘
                │
                ▼
┌──────────────────────────────────────────────────────────────┐
│           ACME DATABASE                                      │
│   • Store employment record (ACTIVE)                         │
│   • Persist audit trail                                      │
│   • Trigger notification pipeline                            │
└──────────────────────────────────────────────────────────────┘
```

## Level 1: Detailed Data Flow (Create Employment)

```
[1] Acme UI → Integration API
Payload:
{
  "employee_name": "John Doe",
  "country": "Germany",
  "role": "Software Engineer",
  "start_date": "2025-11-01",
  "salary": 95000,
  "currency": "EUR"
}

[2] Integration Service
→ Validate schema
→ Transform to Remote format
→ POST /v1/employments

[3] Remote EOR API
→ Creates employment (id=emp_9876)
→ Emits webhook "employment.created"

[4] Webhook Receiver
→ Validates HMAC
→ Publishes to "employment.created" queue

[5] Worker
→ Updates local DB: status=ACTIVE
→ Sends Slack notification

[6] Acme Dashboard
→ Shows “Employment Active” real-time via WebSocket
```

## Level 2: Data Entities

| Entity     | Source | Destination | Description              |
| ---------- | ------ | ----------- | ------------------------ |
| Employment | Acme   | Remote      | Core employment record   |
| Employee   | Acme   | Remote      | Personal information     |
| Contract   | Remote | Acme        | Generated contract PDF   |
| TimeOff    | Remote | Acme        | Vacation/leave requests  |
| Payroll    | Remote | Acme        | Monthly payslips         |
| Document   | Both   | Both        | ID, tax, compliance docs |

##Data Validation Rules

##All POST requests validated via JSON Schema Draft 07

##Dates → ISO 8601 (YYYY-MM-DD)

##Currency → ISO 4217 (USD, EUR, GBP)

##Salary range validation by country config

##Names sanitized (no control chars)
