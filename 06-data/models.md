# Data Models and Schemas — FIXGO

> **What is this?** This document defines the persistence strategy, database engine selections per service, and the core entity schemas for the FIXGO platform.

---

## 1. Database per Service Strategy

Following the architecture principles (ADR-002), each microservice owns its data to ensure strict isolation and independent scaling.

| Service | Primary Database | Usage / Justification |
|---|---|---|
| **Order Service** | MySQL 8.0 | Relational integrity for financial and operational state machines (ACID compliance). |
| **Tracking Service** | PostgreSQL + PostGIS / Redis | PostGIS for spatial queries (finding nearest mechanics). Redis for real-time telemetry caching. |
| **API Gateway** | Redis | Ephemeral storage for rate limiting, session caching, and JWT blocklists. |

---

## 2. Order Service Schemas (MySQL)

### Table: `repair_orders`
Stores the core aggregate for emergency assistance requests.

| Column | Type | Constraints | Description |
|---|---|---|---|
| `id` | VARCHAR(36) | PRIMARY KEY | UUID v4 |
| `driver_id` | VARCHAR(36) | NOT NULL, INDEX | Reference to external identity |
| `mechanic_id` | VARCHAR(36) | NULL, INDEX | Assigned mechanic |
| `status` | VARCHAR(20) | NOT NULL | PENDING, ACCEPTED, EN_ROUTE, COMPLETED, CANCELED |
| `failure_details` | TEXT | NOT NULL | Driver's description of the issue |
| `latitude` | DECIMAL(10,8) | NOT NULL | Initial breakdown location |
| `longitude` | DECIMAL(11,8) | NOT NULL | Initial breakdown location |
| `created_at` | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP | |
| `updated_at` | TIMESTAMP | ON UPDATE CURRENT_TIMESTAMP | |

### Table: `outbox_events` (Outbox Pattern)
Ensures reliable domain event publishing without data loss (ADR-005).

| Column | Type | Constraints | Description |
|---|---|---|---|
| `id` | VARCHAR(36) | PRIMARY KEY | Event UUID |
| `aggregate_id` | VARCHAR(36) | NOT NULL, INDEX | Reference ID (e.g., RepairOrderId) |
| `event_type` | VARCHAR(100) | NOT NULL | E.g., 'ServiceRequested' |
| `payload` | JSON | NOT NULL | Full event data payload |
| `published` | BOOLEAN | DEFAULT FALSE, INDEX | Has it been processed by the relay? |
| `created_at` | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP | |

---

## 3. Tracking Service Schemas (PostgreSQL / Redis)

### Table: `mechanic_locations` (PostgreSQL + PostGIS)
Stores the last known durable location for geospatial querying and matching.

| Column | Type | Constraints | Description |
|---|---|---|---|
| `mechanic_id` | VARCHAR(36) | PRIMARY KEY | Reference to mechanic identity |
| `location` | GEOMETRY(Point, 4326)| NOT NULL, SPATIAL INDEX | Current geospatial coordinates |
| `is_active` | BOOLEAN | DEFAULT FALSE | Is the mechanic available for new orders? |
| `updated_at` | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP | |

### Cache: `active_tracking` (Redis)
High-throughput temporary storage for live WebSocket streaming while a mechanic is en route.

- **Key:** `tracking:order:{orderId}`
- **Value (JSON):** `{"lat": 2.9273, "lng": -75.2818, "timestamp": 1695028400}`
- **TTL:** 1 hour (expires automatically after inactivity to free memory).

---

## 4. Data Retention and Archiving

- **Active Orders:** Kept in the primary MySQL database for fast access.
- **Completed/Canceled Orders:** Moved to cold storage (Data Lake / S3) after 90 days to maintain MySQL query performance.
- **Telemetry Data:** High-frequency Redis points expire automatically. Persistent tracking logs in PostgreSQL are aggregated daily and purged after 30 days.

---
## Key Correlations

- Architecture overview → `05-architecture/overview.md`
- Pattern decisions → `05-architecture/pattern-guide.md`
- Domain entities → `02-domain/entities-and-rules.md`