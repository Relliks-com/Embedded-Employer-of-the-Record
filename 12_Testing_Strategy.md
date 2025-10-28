
# Testing Strategy

## Testing Pyramid
```text
           ▲
           │ End-to-End (5%)
           │  • Full API flows
           │  • Contract tests
           │
           │ Integration (20%)
           │  • Saga orchestration
           │  • Remote API mocking
           │
           │ Unit (75%)
           │  • Services
           │  • Repositories
           │  • Utilities
           ▼
```

# 🧪 Testing & Quality Assurance

## 🧩 Unit Tests

| **Framework** | **Coverage Target** | **Execution** |
|----------------|----------------------|----------------|
| Jest / xUnit  | ≥ 90%                | Run in CI on every PR |

**Example:**
```javascript
test("creates employment successfully", async () => {
  const result = await createEmployment(mockRequest);
  expect(result.status).toBe("PENDING_REMOTE_CREATE");
});
```

## 🔗 Integration Tests

 - Use WireMock to simulate Remote API responses
 - Run Dockerized Redis + Postgres locally
 - Validate Saga execution and compensation logic
 - Assert eventual consistency after retries

## 🤝 Contract Testing

 - Pact contract between Acme and Remote EOR
 - Enforced in CI: consumer driven tests must match provider schema

## 🌐 End-to-End Testing

- Cypress suite → user flows (e.g., hire employee, terminate)
- Smoke tests triggered on every deploy
- Data reset between runs using seed fixtures

## ⚡Performance Testing

| Tool       | Scope               | SLA / Target                     | Profiles                 |
| ---------- | ------------------- | -------------------------------- | ------------------------ |
| **Locust** | Staging Environment | P95 < 2s for employment creation | Normal / Peak / Failover |

## 🛡️ Security Testing

- OWASP ZAP dynamic scans nightly
- SAST (SonarQube) + DAST (ZAP) integration in pipeline
- Manual pentest quarterly

## 🔁 CI/CD Integration

GitHub Actions + CodePipeline

Gates:

 - ✅ All tests pass
 - ✅ SonarQube A rating
 - ✅ No critical CVEs
 - ✅ Code coverage ≥ 90%
