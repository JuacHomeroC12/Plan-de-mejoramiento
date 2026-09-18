  # Domain Events - FIXGO

  > **What to fill in here:** A domain event is a fact that occurred in the business.
  > They are the backbone of asynchronous communication between bounded contexts in FIXGO.
  > The name is ALWAYS in past tense and in the ubiquitous language of the domain.

  ---

  ## What is a domain event?

  A **Domain Event** communicates that something important occurred in the FIXGO business workflow.
  It is an immutable message that describes the fact in past tense.

  - ServiceRequested (A client requested mechanical assistance)
  - MechanicAssigned (A mechanic accepted the service order)
  - UserRegistered (A new client or mechanic registered)
  - ServiceCompleted (The repair order was successfully finalized)

  ### Difference between Command and Event

  | Concept | Intent | Tense | Can fail? |
  |---------|--------|-------|-----------|
  | **Command** | Instruction to do something (e.g., RequestAssistance) | Present | Yes |
  | **Event** | Notification of something that occurred (e.g., ServiceRequested) | Past | No (it already happened) |

  ---

  ## Event catalog

  ### Event: ServiceRequested

  | Field | Value |
  |-------|-------|
  | **Name** | ServiceRequested |
  | **Bounded Context** | Services Context |
  | **Aggregate** | RepairOrder |
  | **Trigger** | Client submits a mechanical assistance request via app/web |
  | **Consumers** | Tracking Context, Notification Context |
  | **Channel (topic)** | fixgo.services.order-created |
  | **Schema version** | v1 |
  | **Delivery guarantee** | At-least-once |

  **Payload (JSON schema):**

  ```json
  {
    "eventId": "550e8400-e29b-41d4-a716-446655440000",
    "eventType": "ServiceRequested",
    "aggregateId": "550e8400-e29b-41d4-a716-446655440001",
    "aggregateType": "RepairOrder",
    "occurredAt": "2026-09-18T10:30:00Z",
    "version": 1,
    "payload": {
      "clientId": "uuid-client",
      "vehicleId": "uuid-vehicle",
      "issueDescription": "Flat tire and engine failure",
      "latitude": 4.5708,
      "longitude": -74.2973
    },
    "metadata": {
      "correlationId": "550e8400-e29b-41d4-a716-446655440002",
      "causationId": "550e8400-e29b-41d4-a716-446655440003",
      "userId": "uuid-client"
    }
  }
  ```

  **Real payload example:**

  ```json
  {
    "eventId": "generated-uuid",
    "eventType": "ServiceRequested",
    "aggregateId": "aggregate-uuid",
    "aggregateType": "RepairOrder",
    "occurredAt": "2026-09-18T10:30:00Z",
    "version": 1,
    "payload": {
      "clientId": "uuid-client",
      "estimatedCost": 150.00
    }
  }
  ```

  **What do consumers do with this event?**

  | Consuming service | Action | Idempotent? |
  |------------------|--------|-------------|
  | Tracking Context | Initializes GPS route monitoring and radar search for nearby mechanics | Yes — uses eventId as idempotency key |
  | Notification Service | Sends push notification (FCM) to mechanics within the coverage radius | Yes — checks if notification was already sent |

  ---

  ## Standard fields for all events

  All events must include these fields in the envelope:

  | Field | Type | Description |
  |-------|------|-------------|
  | eventId | UUID | Unique event ID (for idempotency) |
  | eventType | string | Event name in PascalCase |
  | aggregateId | UUID | ID of the aggregate that generated the event |
  | aggregateType | string | Aggregate type |
  | occurredAt | ISO 8601 | When the business fact occurred |
  | version | integer | Schema version (for evolution) |
  | payload | object | Event data (specific per type) |
  | metadata.correlationId | UUID | For tracing a transaction across services |
  | metadata.causationId | UUID | ID of the event or command that caused this event |
  | metadata.userId | UUID | User who initiated the chain |

  ---

  ## Event flow: Mechanical Assistance Lifecycle

  > Document here the event flows for the main business processes.
  > Use the Event Storming format: orange=event, blue=command, green=view/policy, yellow=aggregate.

  ```text
  Client
    │
    │  RequestAssistance (Command)
    ▼
  Aggregate: RepairOrder
    │
    │  ServiceRequested (Event)
    ├──────────────────────────────────┐
    │                                  ▼
    │                         Service: Tracking
    │                         MechanicAssigned (Event)
    │
    │  ServiceRequested (Event)
    └──────────────────────────────────┐
                                      ▼
                              Service: Notifications
                              NotificationSent (Event)
  ```

  ### Example: Order creation flow

  ```text
  Customer
    │
    │  RequestAssistance (command)
    ▼
  Aggregate: RepairOrder
    │
    │  ServiceRequested (event)
    ├──────────────────────────────────┐
    │                                  ▼
    │                         Service: Tracking
    │                         Radar search for mechanics
    │                         MechanicAssigned (event)
    │
    │  ServiceRequested (event)
    └──────────────────────────────────┐
                                      ▼
                              Service: Notifications
                              Sends push notification to mechanics
  ```

  ---

  ## Schema evolution strategy

  Events are contracts. Changing them in an incompatible way breaks consumers.

  ### What is a compatible change (does not break)?

  - Add a new optional field to the payload
  - Add a new event type
  - Change a required field to optional

  ### What is an incompatible change (breaks)?

  - Remove a field from the payload
  - Change the type of a field (string to number)
  - Change an optional field to required
  - Change the event name

  ### How to evolve a schema without breaking consumers

  **Strategy: Version the event**

  - Step 1: Publish EventV2 (new type with incompatible changes)
  - Step 2: Publish both EventV1 and EventV2 during the migration period
  - Step 3: Migrate consumers to V2 one by one
  - Step 4: Deprecate EventV1 (announce 1 sprint in advance)
  - Step 5: Stop publishing EventV1

  ---

  ## Event summary table

  | Event | Origin context | Topic | Consumers | Version |
  |-------|---------------|-------|-----------|---------|
  | ServiceRequested | Services | fixgo.services.order-created | Tracking, Notifications | v1 |
  | MechanicAssigned | Workshop | fixgo.workshop.mechanic-assigned | Services, Tracking | v1 |
  | ServiceCompleted | Services | fixgo.services.order-completed | Billing, History | v1 |

  ---

  ## Policies — Reactions to events

  A **Policy** (or Saga step) describes what happens automatically when an event arrives.
  It is the logic of "whenever X occurs, do Y".

  ```text
  Event:  ServiceRequested
  Policy: Whenever a ServiceRequested arrives with high priority,
          emit the command NotifyPriorityOrder
  ```

  | Trigger event | Policy | Emitted command | Service |
  |--------------|--------|----------------|---------|
  | ServiceRequested | Whenever priority is urgent, then... | NotifyPriorityOrder | TrackingService |

  ---

  ## Resilience patterns for events

  ### At-least-once delivery + Idempotency

  The message broker guarantees the event is delivered **at least once** but it may be
  delivered more than once (in case of retries). Consumers must be **idempotent**.

  ```typescript
  // Idempotent consumer — stores the processed eventId
  async function processServiceRequestedEvent(event: ServiceRequested): Promise<void> {
    // 1. Check if already processed
    if (await isEventAlreadyProcessed(event.eventId)) {
      logger.info(`Event ${event.eventId} already processed, ignoring`);
      return;
    }

    // 2. Process the event
    await updateModel(event.payload);

    // 3. Mark as processed (in the same transaction)
    await markEventProcessed(event.eventId);
  }
  ```

  ### Dead Letter Queue (DLQ)

  When an event fails after N retries, it goes to the DLQ.

  | Configuration | Recommended value |
  |--------------|------------------|
  | Retries before DLQ | 3-5 |
  | Backoff | Exponential (1s -> 2s -> 4s -> 8s) |
  | DLQ retention | 7 days |
  | Alert | When DLQ has > 0 messages |

  > See DLQ runbook in `09-microservices/services/XX-service/runbook.md`