# Hexagonal Architecture (Ports & Adapters) — FIXGO

> **What is this?** The architectural pattern used in FIXGO to ensure the **business domain is completely independent** of external technologies such as databases, web frameworks, and message brokers.

---

## The problem it solves

In FIXGO, traditional layered architectures mix business logic directly with framework and database calls. Hexagonal architecture isolates the core domain:

- **Primary Adapters (Driving):** Entry points like HTTP controllers or CLI commands that invoke use cases through driving ports.
- **Domain (The Hexagon):** Pure business logic containing aggregates, value objects, domain events, and port interfaces with zero external dependencies.
- **Secondary Adapters (Driven):** Exit points like MySQL persistence, Firebase integration, or event publishers that implement driven ports.

---

## Folder structure for FIXGO Services

src/
├── domain/                         # The hexagon — pure business logic
│   ├── order/
│   │   ├── RepairOrder.ts          # Aggregate Root with business invariants
│   │   ├── RepairOrderId.ts        # Value Object for the ID
│   │   ├── events/
│   │   │   └── ServiceRequested.ts # Domain event
│   │   └── ports/                  # Abstract contracts (interfaces)
│   │       ├── in/
│   │       │   └── CreateOrderPort.ts
│   │       └── out/
│   │           └── OrderRepositoryPort.ts
│
├── application/                    # Use cases — orchestrating domain operations
│   └── order/
│       ├── CreateOrderUseCase.ts   # Implements driving port
│       └── dtos/
│           ├── CreateOrderRequest.ts
│           └── CreateOrderResponse.ts
│
└── infrastructure/                 # External technologies and frameworks
    ├── adapters/
    │   ├── in/
    │   │   └── http/
    │   │       └── OrderController.ts # Primary adapter
    │   └── out/
    │       └── persistence/
    │           └── MySQLOrderRepository.ts # Secondary adapter
    └── config/
        └── container.ts            # Dependency Injection wiring

---

## The Dependency Rule

> **Dependencies always point inward.**
> The domain layer does not import anything from application or infrastructure.

1. **Driving Ports (Input):** Define what the domain can do.
2. **Driven Ports (Output):** Define what the domain needs from the outside world.
3. **Inversion of Control:** Infrastructure components depend on domain ports, never the reverse.

---

## Hexagonal Architecture Checklist for FIXGO

- [ ] `domain/` contains zero imports from frameworks (Express, NestJS, TypeORM, etc.).
- [ ] All repository interfaces reside in `domain/ports/out/`.
- [ ] Use case execution interfaces reside in `domain/ports/in/`.
- [ ] Mappers (`toDomain` / `toPersistence`) are implemented exclusively in `infrastructure/`.
- [ ] Unit tests for aggregates run with zero external dependencies (no real database required).

---

## References and correlations

- Bounded Contexts → `02-domain/domain-map.md`
- Entities and invariants → `02-domain/entities-and-rules.md`
- Domain events → `02-domain/domain-events.md`
- User Stories backlog → `04-requirements/user-stories.md`