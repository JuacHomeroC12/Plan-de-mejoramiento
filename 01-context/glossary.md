# Project Glossary - FIXGO

> **Instructions:** Define here all technical and business terms used in the project.
> This is the official dictionary — if there is ambiguity, this document wins.
> Add terms throughout the project lifecycle, not only at the start.

---

## How to use this glossary

1. Before using a technical or business term in code, docs, or conversations: look it up here.
2. If it's not there: add it with its definition.
3. If there is disagreement about the definition: discuss it as a team and update this document under the Project Leader's guidance.

---

## Domain terms (Mechanical Workshop)

| Term | Definition | Notes / Synonyms |
|------|-----------|-----------------|
| Workshop (Taller) | The physical business entity providing automotive or mechanical repair services. | Do not use "Store" or "Shop". |
| Client (Cliente) | The registered user who owns one or multiple vehicles and requests services. | Synonym: Vehicle Owner. |
| Mechanic (Mecánico) | The employee assigned to perform diagnostic and repair tasks on a vehicle. | |
| Vehicle (Vehículo) | The physical automobile or motorcycle registered in the system for servicing. | |
| Repair Order (Orden de Reparación) | The official document and system record detailing the requested services, assigned mechanic, and current status. | Synonym: Service Order. |
| Spare Part (Repuesto) | A physical inventory item required to complete a repair order. | |
| Appointment (Cita) | A scheduled date and time for a client to bring their vehicle to the workshop. | |

---

## Technical terms of the project

| Term | Definition |
|------|-----------|
| Backend | Server-side logic of FIXGO, developed in Java. |
| Relational Database | Data storage system using structured tables. FIXGO uses MySQL. |
| Identity Provider (IdP) | External service managing user credentials securely. FIXGO uses Firebase Auth. |
| Microservice | Independent service with a single responsibility and its own process. |
| API Gateway | Single entry point to the system that routes requests to the corresponding services. |
| DTO (Data Transfer Object) | An object used to encapsulate data and send it from one subsystem of an application to another (common in Java/Spring). |

---

## Acronyms

| Acronym | Meaning |
|---------|---------|
| ADSO | Análisis y Desarrollo de Software |
| SENA | Servicio Nacional de Aprendizaje |
| IAM | Identity and Access Management |
| JWT | JSON Web Token |
| API | Application Programming Interface |
| CRUD | Create, Read, Update, Delete |
| FR | Functional Requirement |
| NFR | Non-Functional Requirement |
| PR | Pull Request |
| DoD | Definition of Done |
| DoR | Definition of Ready |
| MVC | Model-View-Controller (Architecture Pattern) |