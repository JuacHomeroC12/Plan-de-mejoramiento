# User Stories — Backlog — FIXGO

> **What to fill in here:** The product's User Story backlog.
> Each HU uses the standard format with Acceptance Criteria in Given/When/Then.

---

## Backlog status

| Cut | Sprint | Total HUs | Refined | In progress | Completed |
|-----|--------|-----------|---------|-------------|-----------|
| Cut 1 | Sprint 1-2 | 3 | 2 | 1 | 0 |
| Cut 2 | Sprint 3-4 | 2 | 0 | 0 | 0 |

---

## Epics

| ID | Epic | Description |
|----|------|-------------|
| EP-001 | Emergency Dispatch | Core functionality to request, match, and dispatch roadside assistance. |
| EP-002 | Real-time Tracking | GPS monitoring and status updates for active repair orders. |

---

## User Stories

### HU-SERVICES-001 — Request Emergency Assistance {#HU-SERVICES-001}

**Epic:** EP-001

> **As** a stranded vehicle driver
> **I want** to request immediate roadside assistance and share my GPS coordinates
> **so that** a nearby verified mechanic can locate and assist me quickly

**Acceptance Criteria:**

- Scenario 1: Successful emergency request creation
  - Given the driver is on the emergency request screen
  - When they submit valid failure details and GPS coordinates
  - Then a new RepairOrder is created in PENDING status
  - And the ServiceRequested domain event is triggered

- Scenario 2: Validation error on short description
  - Given the driver attempts to submit a request
  - When the issue description is less than 10 characters long
  - Then the system rejects the request and displays a validation error

**Definition of Done:**
- [x] Code reviewed and approved
- [x] Unit tests written
- [x] Acceptance criteria verified
- [x] API contract updated
- [x] Deployed to staging

| Field | Value |
|-------|-------|
| Story Points | 5 |
| Priority | Must Have |
| Target sprint | Sprint 1 |
| Assigned to | Development Team |
| Status | In Progress |
| Dependencies | None |
| Affected service(s) | order-service |

---

### HU-SERVICES-002 — Real-time Mechanic Tracking {#HU-SERVICES-002}

**Epic:** EP-002

> **As** a stranded driver with an active request
> **I want** to view the real-time location of the dispatched mechanic on a map
> **so that** I have clear visibility and accurate ETA of their arrival

**Acceptance Criteria:**

- Scenario 1: Live tracking view update
  - Given a RepairOrder is in ACCEPTED or IN_PROGRESS status
  - When the assigned mechanic moves and sends location telemetry
  - Then the map updates the mechanic's GPS coordinates in real-time

- Scenario 2: Order closed or cancelled
  - Given the repair order changes to COMPLETED or CANCELLED
  - When the status update is processed
  - Then the real-time tracking stream is closed

| Field | Value |
|-------|-------|
| Story Points | 8 |
| Priority | Must Have |
| Target sprint | Sprint 2 |
| Status | Backlog |
| Dependencies | HU-SERVICES-001 |
| Affected service(s) | tracking-service |

---

## Correlations

- Full template with DoD checklist → `04-requirements/_template-hu.md`
- Non-functional requirements → `04-requirements/non-functional.md`
- Traceability matrix → `04-requirements/traceability-matrix.md`