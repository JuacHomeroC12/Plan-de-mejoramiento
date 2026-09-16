# Backend & Services Documentation Standard - FIXGO

> Defines exactly what documents each backend service or module must have, who writes them,
> when they are created, and when they must be updated. Non-compliance blocks the merge.

---

## Required structure for each service

Each backend module lives in `09-microservices/services/[service-name]/` and MUST have:

```text
09-microservices/services/[service-name]/
├── README.md         ⭐ REQUIRED from Sprint 1
├── data-model.md     ⭐ REQUIRED before creating MySQL/Firebase schemas
├── events.md         ⭐ REQUIRED if the service emits/consumes FCM events
├── decisions.md      🔵 RECOMMENDED — internal technical decisions of the service
└── runbook.md        🟢 REQUIRED before first deploy to staging
```

And its OpenAPI contract in:
```text
07-api/contracts/openapi/[service-name].yaml   ⭐ REQUIRED if it exposes REST endpoints
```

---

## README.md — Service technical sheet

**When to create it:** At the start of the sprint where the service is created.
**Owner:** Project Leader / Developer assigned to the service.
**Update when:** Responsibility, ports, or dependencies between services change.

Minimum content:

| Section | What it must say |
|---------|-----------------|
| Responsibility | One sentence: what it does and what data it is the authoritative owner of. |
| Architecture location | Port, repository, DB engine (MySQL/Firebase), who it communicates with. |
| Responsibilities (what it DOES) | List of concrete responsibilities (e.g., Match mechanic to client). |
| Out of scope (what it does NOT do) | What it delegated and to whom. |
| How to run locally | Exact commands, must work. |
| Related documents | Links to the other files of the service. |

---

## data-model.md — Service data model

**When to create it:** Before the first database script.
**Owner:** Developer assigned to the service.
**Update when:** A table/collection is created or modified.

Minimum content:
- ER diagram (Mermaid) of the service's tables.
- Description of each table with its columns, types, constraints, and purpose.
- Justification of the chosen DB engine (e.g., why MySQL for users, why Firebase for real-time tracking).
- Migration strategy.

**Rule:** A field whose reason for existing is not obvious MUST have a comment in the diagram.

---

## events.md — Service event catalog

**When to create it:** When the service publishes or consumes its first domain event.
**Owner:** Developer assigned to the service.
**Update when:** An event is added, modified, or removed.

Minimum content:
- Table of published events: name, topic, when it is emitted (e.g., FCM Push Notifications).
- Table of consumed events: name, which service it comes from, what action it triggers.
- Payload schema.

---

## decisions.md — Service technical decisions

**When to create it:** When the team makes a non-obvious technical decision about the service.
**Owner:** Whoever made the decision (Tech Lead).
**Update when:** A new decision is made or a previous one is revoked.

Recommended format: miniADR (without the full rigor of an architecture ADR):
```markdown
### Decision: [short name]
**Date:** [date]
**Context:** [what problem was being solved]
**Decision:** [what was decided]
**Consequences:** [known trade-offs]
```

---

## runbook.md — Service operations manual

**When to create it:** Before the first deploy to staging.
**Owner:** Responsible developer.
**Update when:** A new operational issue is discovered or a procedure changes.

Minimum content:
- How to verify the service is healthy.
- Known symptoms and their causes: "If you see X, the problem is Y, the solution is Z".
- How to perform a service rollback.
- Configured alerts and what to do when they fire.

---

## OpenAPI Contract

**When to create it:** Before implementing the service's first endpoint (API-first).
**Owner:** Developer assigned to the service.
**Update when:** An endpoint is added, modified, or removed.

**API-First Rule:** The contract is written BEFORE the code. Contract tests validate
that the code fulfills the contract, not the other way around.

---

## How to add a new service

1. Create the folder structure in `09-microservices/services/[new-service-name]/`.
2. Update the system's architecture documentation.
3. Copy the OpenAPI template to `07-api/contracts/openapi/[new-service-name].yaml`.
4. Create a PR with at least the `README.md` and the sketched API contract.

---

## Correlations

- General documentation rules → `00-governance/documentation-rules.md`