# 02 — Problem Domain — FIXGO

> **What is this?** The mental model of the business. It is not technology — it is understanding
> the problem the FIXGO system solves before writing code. This section comes from Domain-Driven Design (DDD).

## Why this section exists

The most costly mistakes in software are not bugs — they are domain misunderstandings.
When developers do not deeply understand the FIXGO mechanical assistance business:
- They create incorrect abstractions that have to be rewritten
- Names in the code do not match the business's names → permanent confusion
- Microservice boundaries for authentication, orders, and tracking are drawn incorrectly

This section captures domain knowledge **before** designing the architecture.

---

## Key concepts you must know

**Entity:** Domain object with a unique identity (e.g.: a `RepairOrder` identified by its UUID).

**Value Object:** Object with no identity of its own, defined by its attributes (e.g.: `GPSCoordinates`, `Money`).

**Aggregate:** Group of entities and VOs treated as a unit. Only the aggregate root (`RepairOrder`) can be referenced from outside.

**Domain Event:** Something that occurred in the business that other parts of the system must know
(e.g.: `ServiceRequested`, `MechanicAssigned`). They are facts, stated in past tense.

**Bounded Context:** Area of the system where a particular model applies.
Each microservice in FIXGO corresponds to a bounded context.

---

## What is here and how to fill it in

### `domain-map.md` ⭐
Map of all bounded contexts and how they relate.
**Filled in:** user management, services management, and tracking contexts with their upstream/downstream relationships.

**Format:**

## Bounded Contexts

### Services Context
**Responsibility:** Handles roadside mechanical assistance orders
**Main entities:** RepairOrder, Vehicle
**Owning team:** FIXGO Development Team

## Relationship map
User Management (U) -> Downstream -> Services Context -> Downstream -> Tracking & Notifications

| Context A | Relationship | Context B | Description |
|-----------|-------------|-----------|-------------|
| User Management | downstream-of | Services Context | Services consumes user profiles and authentication tokens |

### `entities-and-rules.md` ⭐
Catalog of entities, value objects, and business rules.
**Filled in:** Entity `RepairOrder`, Value Object `GPSCoordinates`, and invariants for FIXGO.

**Format:**

## Entity: RepairOrder
**Belongs to:** Services Context
**Identifier:** UUID

### Attributes
| Attribute | Type | Description | Required | Rules |
|-----------|------|-------------|---------|-------|
| id | UUID | Unique identifier | Yes | Auto-generated |
| issueDescription | string | Failure details | Yes | Min 10 chars |

### Business rules (invariants)
- [x] Description must be at least 10 characters long
- [x] Completed orders cannot transition back to pending

### Behaviors (domain methods)
- `create()`: Initializes a new repair order in PENDING status
- `accept()`: Transitions the order to ACCEPTED when a mechanic takes it

### `domain-events.md` ⭐
List of all events that occur in the domain.
**Filled in:** event name (past tense), triggers, JSON payload schemas, and consumers.

**Format:**

| Event | Triggered by | Data | Consumers | Bounded Context |
|-------|-------------|------|-----------|----------------|
| ServiceRequested | Client submits request | clientId, vehicleId, coordinates | Tracking, Notifications | Services |

---

## Correlations with other sections

| This section feeds... | Why |
|-----------------------|-----|
| `05-architecture/overview.md` | Bounded contexts → microservices (`auth-service`, `order-service`, etc.) |
| `06-data/models.md` | Entities → MySQL tables and Firebase collections |
| `07-api/contracts/` | Domain events → async APIs and topics (`fixgo.services.order-created`) |
| `09-microservices/event-catalog.md` | All domain events are registered there |
| `04-requirements/user-stories.md` | Business rules → acceptance criteria (Gherkin format) |

---

## Recommended tool: Event Storming

**Event Storming** is a domain discovery workshop with sticky notes:
1. 🟠 Orange: Domain events (`ServiceRequested`, `MechanicAssigned`)
2. 🔵 Blue: Commands (`RequestAssistance`, `AcceptOrder`)
3. 🟡 Yellow: Actors (Client, Mechanic, Admin)
4. 🟣 Purple: Policies (automatic dispatch rules)
5. 🟦 Light blue: External systems (Firebase, Push Notifications)

Running an Event Storming session with the team before filling in this section saves weeks of redesign.

---

## Questions this section must answer

- What are the main business entities? (`RepairOrder`, `Vehicle`)
- What rules can NEVER be violated in the system? (Invariants like `INV-001` and `INV-002`)
- What important events occur in the domain? (`ServiceRequested`, `ServiceCompleted`)
- Where are the natural boundaries of the system? (User Management, Services, and Tracking contexts)