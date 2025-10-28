
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

Unit Tests

Framework: Jest / xUnit

Coverage target: ≥ 90%

Run in CI on every PR

Example:

test("creates employment successfully", async () => {
  const result = await createEmployment(mockRequest);
  expect(result.status).toBe("PENDING_REMOTE_CREATE");
});

Integration Tests

Use WireMock to simulate Remote API responses

Run Dockerized Redis + Postgres locally

Validate Saga execution and compensation logic

Assert eventual consistency after retries

Contract Testing

Pact contract between Acme and Remote EOR

Enforced in CI: consumer driven tests must match provider schema

End-to-End Testing

Cypress suite → user flows (e.g., hire employee, terminate)

Smoke tests triggered on every deploy

Data reset between runs using seed fixtures

Performance Testing

Locust (10k virtual users) against staging

SLA: P95 < 2 s for employment creation

Load profiles: normal, peak, failover

Security Testing

OWASP ZAP dynamic scans nightly

SAST (SonarQube) + DAST (ZAP) integration in pipeline

Manual pentest quarterly

CI/CD Integration

GitHub Actions + CodePipeline

Gates:

✅ All tests pass

✅ SonarQube A rating

✅ No critical CVEs

✅ Code coverage ≥ 90%
