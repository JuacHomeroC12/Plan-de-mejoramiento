# Domain Map — Bounded Contexts — FIXGO

> The domain map is the central Domain-Driven Design artifact. It defines the system boundaries and their relationships for FIXGO.

## 1. Domain overview

FIXGO manages the complete cycle of mechanical assistance requests, connecting drivers experiencing vehicle failures with nearby expert workshops and mechanics in real time, from the initial service request through to live tracking, diagnosis, and final service completion.

---

## 2. Identified Bounded Contexts

A Bounded Context is the explicit boundary within which a particular domain model has consistent meaning. Each bounded context has its own Ubiquitous Language.

### Bounded Context: User Management

| Field | Value |
|-------|-------|
| Name | User Management Context |
| Responsibility | Captures user registration, roles, profile management, and session tokens via Firebase Auth |
| Owning team | FIXGO Development Team (Juan David Romero Calderón, Juan Diego Oteca Pedreros, Johan Javier Melo) |
| Microservice(s) | auth-service |
| Database | Firebase Auth / MySQL (User metadata) |
| Ubiquitous Language | Client, Mechanic, Administrator, Credentials, Role |

**Context-specific terms (Ubiquitous Language):**

| Term | Meaning in THIS context | Different in another context? |
|------|------------------------|-------------------------------|
| User | Person registered with credentials and access roles | Yes — in Services it is referred to as "Client" or "Mechanic" |
| Account | Set of login credentials and token state | No |

---

### Bounded Context: Services Management

| Field | Value |
|-------|-------|
| Name | Services Context |
| Responsibility | Handles the creation, lifecycle, and status updates of roadside assistance orders |
| Owning team | FIXGO Development Team (Juan David Romero Calderón, Juan Diego Oteca Pedreros, Johan Javier Melo) |
| Microservice(s) | order-service, vehicle-service |
| Database | MySQL |
| Ubiquitous Language | RepairOrder, Vehicle, IssueDescription, ServiceStatus |

**Context-specific terms (Ubiquitous Language):**

| Term | Meaning in THIS context | Different in another context? |
|------|------------------------|-------------------------------|
| Client | The person requesting assistance for a specific vehicle | Yes — in Auth it is a "User" |
| RepairOrder | The formal transactional record of a mechanical assistance request | No |

---

## 3. Context Map

The Context Map shows relationships between bounded contexts. Relationships define how contexts communicate and who holds the power in the integration.

### Context relationship types

| Type | Symbol | Description | Example |
|------|--------|-------------|---------|
| Upstream → Downstream | U → D | U provides, D consumes. D depends on U. | User Management → Services |
| Open Host Service | OHS | U publishes a published protocol | Event Bus, Firebase Realtime DB |
| Published Language | PL | Explicit shared language | JSON Events, OpenAPI spec |

### Relationships table

| Context A | Relationship | Context B | Communication channel | Contract |
|-----------|-------------|-----------|----------------------|---------|
| User Management | U → D | Services Management | REST API / Firebase JWT | OpenAPI |
| Services Management | U → D | Tracking & Notifications | Event Bus / Realtime DB | AsyncAPI / JSON Events |

---

## 4. Core Domain, Supporting, Generic

Domain-Driven Design classifies subdomains by their strategic value:

| Type | Description | Investment | Example |
|------|-------------|-----------|---------|
| Core Domain | Where the business competitive advantage lies. What differentiates us. | MAXIMUM — build, don't buy | Real-time mechanic matching and tracking |
| Supporting Subdomain | Necessary for the core but not differentiating. Can be outsourced. | MEDIUM | Service Order lifecycle management |
| Generic Subdomain | Commodity. Off-the-shelf solution exists. | MINIMUM — buy/use OSS | Authentication via Firebase |

### Classification of this project's bounded contexts

| Bounded Context | Type | Justification |
|----------------|------|---------------|
| Tracking & Notifications | Core | The real-time geolocation matching is the primary value proposition of FIXGO |
| Services Management | Supporting | Manages the workflow of orders once created |
| User Management | Generic | Standard authentication handled via Firebase |

---

## 5. Modeling decisions

### How were these decisions made?

- Event Storming session: August 2026, FIXGO Development Team (Juan David Romero Calderón, Juan Diego Oteca Pedreros, Johan Javier Melo)
- Tool used: Lucidchart / Draw.io
- Map iterations: v1 (Initial architectural design), v2 (Refined microservices boundaries)

### Key decisions and discarded alternatives

| Decision | Discarded alternative | Reason |
|----------|----------------------|--------|
| Separate User and Services contexts | Monolithic domain model | Business logic evolves at different rates and requires independent scaling |

---

## 6. How to update this map

1. Before adding a new microservice, verify whether it belongs to an existing bounded context.
2. If a context's ubiquitous language is changing, review whether the context should be split.
3. Run an Event Storming session every time the domain changes significantly.
4. The context map MUST be synchronized with the system-level diagrams.