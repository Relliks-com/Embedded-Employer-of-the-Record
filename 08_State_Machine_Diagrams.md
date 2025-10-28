
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


---

### **09_Deployment_Architecture.md**
```markdown
# Deployment Architecture

## AWS Deployment Model
```text
┌────────────────────────────────────────────────────────────┐
│                       AWS Cloud                            │
└────────────────────────────────────────────────────────────┘
```

| Layer      | Service               | Purpose                           |
| ---------- | --------------------- | --------------------------------- |
| Compute    | **ECS Fargate**       | Host Integration and API services |
| Storage    | **RDS PostgreSQL**    | Persistent relational data        |
| Cache      | **ElastiCache Redis** | Low-latency cache and rate limits |
| Queue      | **SQS**               | Event-driven async processing     |
| Secrets    | **Secrets Manager**   | Credential storage                |
| Networking | **ALB + VPC**         | Secure private subnets            |
| CI/CD      | **CodePipeline**      | Build & deploy automation         |


# Deployment Topology

                     Internet
                        │
                        ▼
             ┌─────────────────────┐
             │ CloudFront CDN      │
             └────────┬────────────┘
                      │
                      ▼
             ┌─────────────────────┐
             │ API Gateway (Kong)  │
             └────────┬────────────┘
                      │
                      ▼
             ┌───────────────────────────────┐
             │ ECS Fargate Cluster            │
             │  • Integration Service         │
             │  • Webhook Receiver            │
             │  • Worker Service              │
             └────────┬────────────┬──────────┘
                      │            │
                      ▼            ▼
          ┌────────────────┐  ┌────────────────┐
          │  RDS PostgreSQL│  │ ElastiCache    │
          │  Multi-AZ      │  │ Redis Cluster  │
          └────────────────┘  └────────────────┘
                      │
                      ▼
              ┌───────────────┐
              │ S3 Storage    │
              │ Documents,    │
              │ Audit logs    │
              └───────────────┘

Deployment Strategy

Blue-Green Deployments: via CodeDeploy hooks

Zero Downtime: pre-warmed containers and connection draining

Autoscaling:

CPU > 70% → scale out

Queue depth > 500 → add worker replicas

Idle 10m → scale in

Regions

Primary: us-east-1

DR: eu-west-1 (RPO: 15 min, RTO: 30 min)

Networking & Security

Private subnets for DB/cache

Security groups restrict access to ALB only

NACLs limit external ingress to ports 443/80 only

Secrets injected at runtime via IAM task roles

WAF & Shield Advanced for DDoS protection
