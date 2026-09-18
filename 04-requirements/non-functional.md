# Non-Functional Requirements (NFR) — FIXGO

> NFRs define the **qualities of the system** — not what it does but how well it does it.
> The golden rule: every NFR must have a metric for FIXGO.

---

## NFR-001: Performance

| Attribute | Metric | Test condition |
|-----------|--------|---------------|
| P95 latency — critical endpoints | < 300ms | Under 500 RPS load |
| P99 latency — critical endpoints | < 500ms | Under 500 RPS load |
| P95 latency — non-critical endpoints | < 1000ms | Normal load |
| Minimum throughput | 200 RPS | Without degradation |
| Service startup time | < 30 seconds | Cold start |

**Defined critical endpoints:**
- `POST /api/v1/repair-orders` — Critical for immediate emergency dispatch response.
- `GET /api/v1/repair-orders/{id}/tracking` — Critical for live GPS status monitoring.

**Load testing tools:** k6, Apache JMeter.
**Where is it validated?** CI/CD staging pipeline.

---

## NFR-002: Availability

| Environment | SLO | Maintenance window | Max downtime/month |
|------------|-----|-------------------|-------------------|
| Production | 99.9% | Sundays 2am-4am | 44 minutes |
| Staging | 95% | No restriction | 36 hours |

**Monthly error budget in production:** 44 minutes.
**Error Budget policy:** If > 50% of error budget is consumed in the first half of the month, feature deploys are frozen.

**Health checks:**
- `GET /health` — Liveness probe.
- `GET /health/ready` — Readiness probe (DB and Firebase connected).

---

## NFR-003: Scalability

| Scenario | Expected behavior |
|---------|------------------|
| Gradual load growth | Horizontal auto-scaling when CPU > 70% |
| Sudden spike | System scales in < 2 minutes |
| Load reduction | Scale-down without interrupting active traffic |
| Horizontal scaling limit | Up to 10 instances per microservice |

**Strategy:** Stateless horizontal scaling with Redis for sessions and MySQL for persistence.

---

## NFR-004: Security

### Authentication and Authorization
- All private endpoints require a valid JWT in the `Authorization: Bearer <token>` header.
- JWT tokens expire in **1 hour**; refresh tokens valid for **7 days**.
- RBAC implemented for clients, mechanics, and administrators.

### Data transmission & Sensitive data
- HTTPS mandatory in production (TLS 1.2+).
- Passwords hashed with bcrypt (cost factor ≥ 12). Secrets stored strictly in environment variables.

---

## NFR-005: Observability

| Pillar | Requirement | Tool |
|--------|------------|------|
| Logs | Structured JSON format + Correlation ID | Winston / Logback |
| Metrics | RED per endpoint | Prometheus + Grafana |
| Traces | End-to-end distributed traces | OpenTelemetry + Jaeger |
| Alerts | Alert in < 5 min on SLO violation | Alertmanager |

---

## NFR-006: Maintainability

| Metric | Target |
|--------|--------|
| Test coverage | ≥ 80% of lines (≥ 90% in domain logic) |
| Cyclomatic complexity | ≤ 10 per function |
| Technical debt resolution | < 1 sprint |
| Onboarding time | < 1 hour |

---

## NFR-007: Portability & Disaster Recovery

- **Portability:** All microservices deployed as Docker images compatible with Kubernetes 1.28+.
- **Disaster Recovery:** Primary database failure RTO < 5 minutes, RPO < 1 second with synchronous replication.

---

## Correlations

- Detailed SLOs → `13-operations/README.md`
- Security policies → `00-governance/security-policy.md`