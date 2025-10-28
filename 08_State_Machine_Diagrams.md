
# State Machine Diagrams

## Employment Lifecycle State Machine
```text
┌──────────────────────────────────────────────────────────────┐
│                   EMPLOYMENT LIFECYCLE                       │
└──────────────────────────────────────────────────────────────┘

[ DRAFT ]
   │
   │ submit()
   ▼
[ PENDING_REMOTE_CREATE ]
   │
   │ remote.create_success()
   ▼
[ PENDING_EMPLOYEE_DETAILS ]
   │
   │ employee.submit_details()
   ▼
[ PENDING_VERIFICATION ]
   │
   │ remote.verification_success()
   ▼
[ PENDING_CONTRACT_SIGNATURE ]
   │
   │ employee.sign_contract()
   ▼
[ ACTIVE ]
   │
   │ termination_requested()
   ▼
[ TERMINATION_PENDING ]
   │
   │ remote.terminate_success()
   ▼
[ TERMINATED ]
```

##Transitions

| Event                           | From                       | To                         | Action              |
| ------------------------------- | -------------------------- | -------------------------- | ------------------- |
| `submit()`                      | Draft                      | Pending Remote Create      | Create local record |
| `remote.create_success()`       | Pending Remote Create      | Pending Employee Details   | Save remote ID      |
| `employee.submit_details()`     | Pending Employee Details   | Pending Verification       | POST details        |
| `remote.verification_success()` | Pending Verification       | Pending Contract Signature | Notify employee     |
| `employee.sign_contract()`      | Pending Contract Signature | Active                     | Activate employment |
| `termination_requested()`       | Active                     | Termination Pending        | Notify Remote       |
| `remote.terminate_success()`    | Termination Pending        | Terminated                 | Close employment    |


##Compensation Flows

```
create_remote_employment → delete_remote_employment
create_local_employment → soft_delete_local_employment
update_remote_status → retry_remote_update
```

##Time-Based State Transitions

##Auto-expire draft after 7 days of inactivity

##Auto-escalate verification after 72h pending

##Auto-notify HR for unsigned contracts >5 days


NACLs limit external ingress to ports 443/80 only

Secrets injected at runtime via IAM task roles

WAF & Shield Advanced for DDoS protection
