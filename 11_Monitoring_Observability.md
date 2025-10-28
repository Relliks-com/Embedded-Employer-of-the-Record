# Monitoring & Observability

## Metrics
| Domain | Metric | Source | Frequency |
|---------|---------|---------|------------|
| API Latency | `remote_api_latency_seconds` | Prometheus | 10 s |
| Throughput | `http_requests_total` | DataDog | 1 min |
| Errors | `remote_api_errors_total` | DataDog | 1 min |
| Queue | `sqs_messages_visible` | CloudWatch | 1 min |
| DB | `connections_active` | RDS Enhanced Metrics | 1 min |

---

## Tracing
- **OpenTelemetry** instrumentation  
- Context propagation through HTTP headers (`traceparent`, `correlation-id`)  
- Visualized in **DataDog APM**  
- Sample rate: 10% of traffic (100% for error paths)

---

## Logging
- **Structured JSON Logs** via Winston (Node.js) / Serilog (.NET)
- Output to stdout → CloudWatch → S3 (7 days hot retention)
- Log fields: timestamp, level, request_id, user_id, latency_ms
- PII masking applied before log write

Example:
```json
{
  "timestamp": "2025-10-28T06:10:20Z",
  "level": "WARN",
  "service": "webhook-receiver",
  "message": "Duplicate webhook ignored",
  "event_id": "evt_123"
}
```

## 📊 Monitoring & Observability

### 📈 Dashboards

| Tool        | Purpose |
|--------------|----------|
| **DataDog**  | Service Performance Overview |
| **CloudWatch** | Infrastructure Health |
| **Grafana**  | Custom dashboards for integration latency and error rates |


### Alerts
| Source          | Threshold       | Action                   |
| --------------- | --------------- | ------------------------ |
| CPU Usage       | > 80% for 5 min | Scale ECS Service        |
| API Errors      | > 2% for 10 min | PagerDuty Critical       |
| Queue Depth     | > 1000          | Add Worker Replica       |
| Database Lag    | > 30 s          | Notify Ops               |
| Security Events | any critical    | Security Incident Bridge |

### 🤖 Synthetic Monitoring

- **Pingdom** checks every **5 minutes** → hits health endpoint (`/_health`)  
- **Postman collection runs** as **CI/CD smoke tests** after each deployment  
- **Canary tests** simulate **employment creation flow** every **15 minutes**  

