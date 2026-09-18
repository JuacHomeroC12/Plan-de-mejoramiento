# User Stories — FIXGO

> **What is this?** The formalization of user needs into User Stories (HU) following the MoSCoW prioritization method and Gherkin acceptance criteria format.

## Why this section exists

User stories translate high-level product needs and domain rules into concrete, testable requirements. They serve as the direct bridge between product definition and software architecture.

---

## User Stories Catalog

## HU-SERVICES-001: Request Emergency Assistance
**As** a stranded vehicle driver
**I want** to request immediate roadside assistance and share my GPS coordinates
**So that** a nearby verified mechanic can locate and assist me quickly

### Acceptance criteria
- [ ] **AC1:** Given that the driver is on the emergency screen, when they submit valid failure details and GPS coordinates, then a new `RepairOrder` is created in PENDING status.
- [ ] **AC2:** Given that the driver attempts to submit a request, when the issue description is less than 10 characters long, then the system rejects the request and displays a validation error.
- [ ] **AC3:** Given that the system receives a valid request, when the order is registered, then the `ServiceRequested` domain event is triggered.

### Technical notes
**Responsible service(s):** Services Context / Order Microservice
**Endpoint(s) implemented:** POST `/api/v1/repair-orders`
**Events generated:** `ServiceRequested`
**Required permissions:** Authenticated Client Role

**Estimation and priority:**
- **Story Points:** 5
- **Priority:** High
- **Target sprint:** Sprint 1
- **Dependencies:** none