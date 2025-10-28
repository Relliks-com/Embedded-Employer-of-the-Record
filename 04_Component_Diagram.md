# C4 Level 3: Component Diagram

## Integration Service - Component Architecture
```text
┌───────────────────────────────────────────────────────────────────────────────────┐
│                         INTEGRATION SERVICE                                       │
│                      Component Architecture                                       │
└───────────────────────────────────────────────────────────────────────────────────┘

```
## API Layer
### Employment Controller 
*(Express Router / FastAPI Router)*

Handles API endpoints:

```bash
POST   /api/v1/employments                 → createEmployment()
GET    /api/v1/employments/:id             → getEmployment()
PATCH  /api/v1/employments/:id             → updateEmployment()
POST   /api/v1/employments/:id/reconcile   → reconcile()
GET    /api/v1/employments                 → listEmployments()
```

## Middleware
- Authentication (JWT validation)
- Request validation (JSON Schema)
- Rate limiting (Token Bucket)
- Correlation ID injection
- Structured error handling

## Saga Orchestrator
Manages distributed transactions across local + remote systems.

### Responsibilities
- Generate idempotency keys
- Maintain transaction state machine
- Coordinate compensating actions
- Implement exponential backoff retries
- Persist saga state

## States
```
INITIATED
  → PENDING_REMOTE_CREATE
  → REMOTE_CREATE_SUCCESS / FAILED
  → COMPENSATING → COMPLETED / FAILED
```

## 🧠 Supporting Components
### Remote API Client
Handles Remote EOR API interaction.

Implements: 
  - OAuth2.0, 
  - retry/circuit breaker logic, 
  - request signing.

## State Machine Engine

Ensures valid employment lifecycle transitions.

Example transitions:

```
PENDING_REMOTE_CREATE
   → PENDING_EMPLOYEE_DETAILS
   → PENDING_VERIFICATION
   → PENDING_CONTRACT_SIGNATURE
   → ACTIVE
   → TERMINATED
```
### Repository Layer

Encapsulates database operations.

 - EmploymentRepository
 - SyncStateRepository
 - ProcessedEventRepository

### Features:

 - Optimistic locking (version column)
 - Audit trail
 - Soft delete


## 🧱Infrastructure Components
### Cache Manager
Manages Redis cache with write-through and invalidation strategies.

Keys:

 - employment:{id}
 - oauth:token
 - ratelimit:{ip}

### Notification Manager

Manages outbound events:

 - WebSocket (real-time updates)
 - Email (SendGrid)
 - Slack
 - SMS (Twilio)

### Metrics Collector
Exports to Prometheus & DataDog:

 - remote_api_latency_seconds
 - employment_created_total
 - sync_drift_detected_total
 - circuit_breaker_state

### Logger
Structured JSON logs with correlation IDs, e.g.:
```
{
  "timestamp": "2025-10-28T10:30:00Z",
  "level": "INFO",
  "service": "integration-service",
  "message": "Employment created",
  "employment_id": "123",
  "remote_id": "emp_abc"
}
```
