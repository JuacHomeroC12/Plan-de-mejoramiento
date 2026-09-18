# System Scope - FIXGO

> **Why this document exists:** Scope prevents scope creep and aligns expectations.
> It is equally important to define what the system does NOT do as what it does.
> Review this document at the start of each planning cycle.

---

## In Scope

What the system **DOES build and maintain**:

### MVP Features

| # | Feature | Description | Responsible service |
|---|---------|-------------|---------------------|
| 1 | User Registration & Auth | Management of accounts, roles, and Firebase JWT tokens[cite: 1] | auth-service[cite: 1] |
| 2 | Vehicle Management | Registration, updating, and associating vehicles to clients[cite: 1] | vehicle-service[cite: 1] |
| 3 | Service Requests | Creation, updating, and cancellation of mechanical assistance orders[cite: 1] | order-service[cite: 1] |
| 4 | Real-Time Monitoring | Live tracking of mechanic location and estimated time of arrival (ETA)[cite: 1] | tracking-service[cite: 1] |

### Included integrations

| External system | Integration type | Purpose |
|----------------|-----------------|---------|
| Firebase Auth / Realtime DB | SDK / Cloud API | Authentication, session tokens, and instant data synchronization[cite: 1] |
| Google Maps API | REST API / SDK | Geolocation, route calculation, and interactive maps[cite: 1] |
| Firebase Cloud Messaging (FCM) | Cloud API | Pushing real-time status notifications to mobile devices[cite: 1] |

### Environments being built

| Environment | Purpose |
|-------------|---------|
| Local | Development on the developer's machine using local MySQL instances |
| Development (dev) | Integration testing and component validation |
| Staging | Pre-production testing and evaluation by the project team |
| Production | Live deployment for workshop and client operations |

---

## Out of Scope

What the system **does NOT build** in this version and why:

| # | What is out of scope | Reason | Future version? |
|---|---------------------|--------|----------------|
| 1 | Physical vehicle repair operations | Handled physically by workshop mechanics, not software[cite: 1] | No (Out of software scope) |
| 2 | In-app e-commerce for spare parts | Focus is strictly on service requests and roadside assistance[cite: 1] | Yes — v2.0 |
| 3 | Direct integration with insurance companies | Requires specialized legal and external contracts[cite: 1] | Pending evaluation |
| 4 | Automated online payment gateway | Initial version focuses on service coordination rather than payment processing[cite: 1] | Yes — v2.0 |
| 5 | Voice assistant integrations | Out of MVP boundaries[cite: 1] | Future roadmap |
| 6 | AI-driven predictive maintenance | Requires advanced telemetry models beyond initial requirements[cite: 1] | Future roadmap |

### What another system / team handles (and why not us)

| Feature | Who builds it | Why not us |
|---------|--------------|-----------|
| User Credential Storage | Firebase Auth (Google) | Secure delegation of password hashing and token generation[cite: 1] |
| Map Rendering & Geocoding | Google Maps API | Leveraging industry-standard cartography instead of custom mapping |

---

## Scope assumptions

> These assumptions are taken to be true. If they change, the scope must be renegotiated.

| # | Assumption | Consequence if false |
|---|-----------|---------------------|
| 1 | Google Maps API remains available with active quotas | Geolocation and route tracking features would fail[cite: 1] |
| 2 | Users and mechanics have active internet connectivity (minimum 3G/4G) | Real-time synchronization and live maps would be restricted[cite: 1] |
| 3 | Firebase infrastructure maintains its SLA uptime | Authentication and database sync could experience interruptions[cite: 1] |

---

## Constraints

| Type | Description |
|------|-------------|
| **Time** | Developed during the current SENA ADSO training period and improvement plan |
| **Technology** | Backend in Java, Relational Database in MySQL, and Authentication via Firebase[cite: 1] |
| **Regulatory** | Must comply with data protection regulations and institutional guidelines[cite: 1] |
| **Team** | Developed by the official FIXGO team: Johan Andrés Liñan Esquivel, Juan David Romero Calderon, Gabriel Tijaro Jimenez, Mateo Esteban Ramirez Garzon[cite: 1] |

---

## External dependencies

| Dependency | Team / Provider | Required date | Status |
|-----------|----------------|--------------|--------|
| Firebase Project Setup | Google Cloud / Firebase | Active | 🟢 Available |
| Google Maps API Keys | Google Cloud Platform | Active | 🟢 Available |

---

## How to update the scope

The scope can change, but the change has a process:

1. Document the proposed change in this file
2. Evaluate the impact on schedule and effort
3. Obtain approval from the Project Leader and Instructor
4. Update the corresponding technical documentation

---

## Correlations

- Term glossary → `01-context/glossary.md`[cite: 1]
- System overview → `01-context/overview.md`[cite: 1]