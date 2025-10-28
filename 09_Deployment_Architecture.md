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

## 🧭Deployment Strategy

 - Blue-Green Deployments: via CodeDeploy hooks
 - Zero Downtime: pre-warmed containers and connection draining

## Autoscaling:

 - CPU > 70% → scale out
 - Queue depth > 500 → add worker replicas
 - Idle 10m → scale in

## 🌍Regions
| Region Type       | Region    | RPO    | RTO   |
| ----------------- | --------- | ------ | ----- |
| Primary           | us-east-1 | ______ | _____ |
| Disaster Recovery | eu-west-1 | 15 min | 30 min|


## 🔒Networking & Security

 - Private subnets for DB/cache
 - Security groups restrict access to ALB only
