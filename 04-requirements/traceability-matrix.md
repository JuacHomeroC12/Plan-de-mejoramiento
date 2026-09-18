# Traceability Matrix — FIXGO

> Traceability connects every line of code to its business justification.
> It allows answering: "Why does this function exist?" and "Which HU covers this part of the system?"

---

## FR → HU → Test → Service matrix

| FR ID | FR Description | HU(s) | Tests that verify it | Service | Status |
|-------|---------------|-------|---------------------|---------|--------|
| FR-001 | System allows user registration and authentication | HU-SERVICES-001 | `auth.register.spec.ts` | auth-service | 🟡 In progress |
| FR-002 | System allows requesting emergency roadside assistance | HU-SERVICES-001 | `order.create.spec.ts` | order-service | ✅ Done |
| FR-003 | System tracks GPS coordinates of active repair orders | HU-SERVICES-001 | `tracking.spec.ts` | tracking-service | 🔴 Pending |

---

## NFR → Validation matrix

| NFR ID | Description | How it is validated | Tool | Status |
|--------|-------------|-------------------|------|--------|
| NFR-001 | P95 latency < 300ms under load | Load test in staging pipeline | k6 | 🟡 In progress |
| NFR-002 | 99.9% system availability | SLO monitoring | Grafana | 🟡 Monitoring |
| NFR-004 | JWT authentication enforced | Security contract test | Postman + OWASP ZAP | 🔴 Pending |

---

## Inverse traceability: HU → FR

| HU | Title | FR(s) it implements | Sprint |
|----|-------|---------------------|--------|
| HU-SERVICES-001 | Request Emergency Assistance | FR-001, FR-002 | Sprint 1 |

---

## Status legend

| Status | Meaning |
|--------|---------|
| ✅ Done | Implemented, tested, and verified |
| 🟡 In progress | Under development in the current sprint |
| 🔴 Pending | In the backlog, not started |
| ⏸ Blocked | Has an external blocker |
| ❌ Cancelled | Removed from scope |

---

## Identified gaps (requirements without coverage)

| Gap type | Description | Required action | Owner | Date |
|----------|-------------|----------------|-------|------|
| HU without test | HU-SERVICES-001 requires automated integration tests | Add test before sprint review | QA / Dev | 2026-03-30 |

---

## Correlations

- User Stories → `04-requirements/user-stories.md`
- Non-Functional Requirements → `04-requirements/non-functional.md`
- Definition of Done → `00-governance/definition-of-done.md`