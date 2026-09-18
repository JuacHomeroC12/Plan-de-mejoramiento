# Entities, Value Objects, and Business Rules - FIXGO

> The building blocks of the domain following the DDD tactical model for FIXGO.
> This document translates domain knowledge into code models.

---

## Tactical DDD concepts

### Entity
An **Entity** is an object defined by its identity, not its attributes.
Two entities are equal if they have the same ID, even if all their other attributes differ.

- Entity: RepairOrder (two orders with different issues are distinct by their ID)
- Entity: Vehicle (changes state or owner but remains the same vehicle entity)

### Value Object (VO)
A **Value Object** is an object defined by its attributes; it has no identity of its own.
It is immutable — if an attribute changes, it is a new VO.

- Value Object: GPSCoordinates (Latitude: 4.5708, Longitude: -74.2973)
- Value Object: Money (COP 150,000.00)
- Value Object: IssueDescription (Flat tire and engine failure text details)

### Aggregate
An **Aggregate** is a cluster of entities and VOs treated as a unit.
An **Aggregate Root** serves as the entry point — internal objects can only be accessed through the root.

RepairOrder (Aggregate Root)
  ├── VehicleId (Reference ID)
  ├── GPSCoordinates (Value Object)
  └── ServiceStatus (Value Object/Enum)

**Golden rule of the Aggregate:** Transactions do not cross aggregate boundaries.

### Business Rules
**Business Rules** (invariants) are the constraints the domain must always satisfy.
They live in the Aggregate Root and are validated on every operation.

---

## System entities

### Entity: RepairOrder

**Context:** Services Context

**Description:** Represents a formal roadside mechanical assistance request created by a client.

**Attributes:**

| Attribute | Type | Description | Required | Rules |
|-----------|------|-------------|---------|-------|
| id | UUID | Unique identifier | Yes | Auto-generated on creation |
| clientId | UUID | Client who requested the service | Yes | Must reference a valid user |
| vehicleId | UUID | Vehicle needing assistance | Yes | Must reference an active vehicle |
| issueDescription | string | Description of the mechanical failure | Yes | Minimum 10 characters |
| status | RepairOrderStatus | Current lifecycle state | Yes | PENDING, ACCEPTED, IN_PROGRESS, COMPLETED, CANCELLED |
| createdAt | DateTime | Creation date | Yes | Immutable, set on creation |
| updatedAt | DateTime | Last modification | Yes | Updated automatically |

**Lifecycle / States:**

PENDING -> (AssignMechanic) -> ACCEPTED -> (StartWork) -> IN_PROGRESS -> (Complete) -> COMPLETED
   |                              |                            |
   +----------(Cancel)------------+-----------(Cancel)---------+
                                  v
                              CANCELLED

| State | Description | Allowed transitions |
|-------|-------------|---------------------|
| PENDING | Request submitted, awaiting mechanic acceptance | -> ACCEPTED, -> CANCELLED |
| ACCEPTED | Mechanic accepted the order and is en route | -> IN_PROGRESS, -> CANCELLED |
| IN_PROGRESS | Mechanic is actively performing repairs | -> COMPLETED, -> CANCELLED |
| COMPLETED | Service successfully finalized | Terminal state |
| CANCELLED | Order cancelled by client or system | Terminal state |

**Invariants (Business rules that MUST ALWAYS hold):**

- INV-001: Order requires description
  - Rule: issueDescription cannot be empty or less than 10 characters
  - Violation: Throws DomainException if description is invalid
  - Implementation: Validated in the static factory creation method
- INV-002: Status transitions
  - Rule: An order in COMPLETED status cannot transition back to PENDING
  - Violation: DomainException is thrown on invalid state change
  - Implementation: State machine check inside transition methods

**Code example (TypeScript):**

class RepairOrder {
  private constructor(
    private readonly id: string,
    private clientId: string,
    private vehicleId: string,
    private issueDescription: string,
    private status: string
  ) {}

  static create(clientId: string, vehicleId: string, issueDescription: string): RepairOrder {
    if (!issueDescription || issueDescription.length < 10) {
      throw new Error('INV-001: Issue description must be at least 10 characters long');
    }
    return new RepairOrder(crypto.randomUUID(), clientId, vehicleId, issueDescription, 'PENDING');
  }

  accept(): void {
    if (this.status !== 'PENDING') {
      throw new Error('INV-002: Only a PENDING order can be accepted');
    }
    this.status = 'ACCEPTED';
  }
}

---

## System Value Objects

### Value Object: GPSCoordinates

**Description:** Represents geographic latitude and longitude coordinates for location tracking.

**Attributes:**

| Attribute | Type | Description |
|-----------|------|-------------|
| latitude | number | Geographical latitude (-90 to 90) |
| longitude | number | Geographical longitude (-180 to 180) |

**Validation rules:**

- Latitude must be between -90 and 90.
- Longitude must be between -180 and 180.

**Example:**

class GPSCoordinates {
  constructor(public readonly latitude: number, public readonly longitude: number) {
    if (latitude < -90 || latitude > 90) throw new Error('Invalid latitude');
    if (longitude < -180 || longitude > 180) throw new Error('Invalid longitude');
  }
}

---

## System Aggregates

### Aggregate: RepairOrderAggregate

**Aggregate Root:** RepairOrder

**Internal entities:**
- ServiceItem — details specific mechanical tasks performed.

**Value Objects:**
- GPSCoordinates, IssueDescription, RepairOrderStatus

**Aggregate invariants:**
- AGGR-INV-001: A RepairOrder must always be linked to a valid client and vehicle.
- AGGR-INV-002: Service items cannot be modified once the order is COMPLETED.

**Why do these objects form an aggregate?**
An Order and its location coordinates must remain transactionally consistent; tasks cannot exist independently outside the scope of the repair request.

---

## Summary table of tactical building blocks

| Name | Type | Bounded Context | Aggregate Root? |
|------|------|----------------|----------------|
| RepairOrder | Entity | Services Context | Yes |
| Vehicle | Entity | Services Context | No (inside Services) |
| GPSCoordinates | Value Object | Shared / Tracking | N/A |
| IssueDescription | Value Object | Services Context | N/A |
| MechanicMatchingService | Domain Service | Workshop / Tracking | N/A |

---

## Domain Services

A **Domain Service** is business logic that does not naturally belong to any single entity.

class MechanicMatchingService {
  findNearbyMechanics(clientLocation: GPSCoordinates, radiusKm: number): any[] {
    return [];
  }
}

---

## Correlation with code

| Domain artifact | Package / folder in code | File |
|----------------|--------------------------|------|
| Aggregate Root `RepairOrder` | `src/domain/services/` | `RepairOrder.ts` |
| Value Object `GPSCoordinates` | `src/domain/shared/` | `GPSCoordinates.ts` |
| Domain Service `MechanicMatchingService` | `src/domain/services/services/` | `MechanicMatchingService.ts` |
| Repository `RepairOrderRepository` | `src/domain/services/ports/` | `RepairOrderRepository.ts` |  