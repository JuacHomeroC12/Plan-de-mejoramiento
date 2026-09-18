# 03 — Product Definition — FIXGO

> **What is this?** The answer to "what are we going to build?". It is not "how" — that comes in
> architecture. Here the validated problem, product vision, and build plan are defined for FIXGO.

## Why this section exists

Without a clear product definition for FIXGO:
- The team builds features nobody asked for
- Scope grows out of control (scope creep)
- There is no way to know whether the project was successful

This section is the contract between the team and stakeholders about **what will be built and why**.

---

## What is here and how to fill it in

### `problem-framing.md` ⭐ (Start here)
Articulates the problem before proposing solutions.
**Filled in:** vehicle owners stranded on highways or urban roads facing sudden mechanical breakdowns.

**Format:**

## The problem
**Who has it?** Vehicle owners and stranded drivers in Colombia
**What problem do they have?** Finding reliable, immediate roadside assistance and transparent pricing
**When does it occur?** During sudden mechanical breakdowns far from a trusted workshop
**What is the impact?** Hours of stranded exposure, overpaid emergency rates, and severe stress
**How do they solve it today?** Yellow pages, phone directories, and informal WhatsApp recommendations

## Why it is worth solving
To eliminate the anxiety, high cost, and unpredictability of roadside emergencies through instant digital matching.

### `vision.md` ⭐
The product's north star in 1-2 sentences.
**Filled in:** For vehicle owners and stranded drivers in Colombia who experience unexpected mechanical breakdowns, the FIXGO platform is a roadside mechanical assistance system that connects drivers with verified nearby mechanics through real-time GPS tracking and transparent pricing.

### `roadmap.md`
Delivery plan over time for FIXGO.
**Filled in:** 

## Phase 1 — MVP (Q1 2026)
- Repair order creation
- GPS tracking of mechanics
- Basic user and driver profiles

## Phase 2 — Iteration (Q2 2026)
- Push notifications
- Integrated chat system
- Automated mechanic matching

---

## User Story format

## HU-SERVICES-001: Request Emergency Assistance
**As** a stranded vehicle driver
**I want** to request immediate roadside assistance and share my GPS coordinates
**So that** a nearby verified mechanic can locate and assist me quickly

### Acceptance criteria
- [ ] AC1: Given the user is on the request screen, when they submit failure details and location, then a new `RepairOrder` is created in PENDING status.
- [ ] AC2: System validates that the issue description is at least 10 characters long.

### Technical notes
Uses Firebase Authentication for user identity and MySQL for storing repair order state.

**Estimation:** 5 SP  **Priority:** High

---

## Correlations with other sections

| This section feeds... | Why |
|-----------------------|-----|
| `04-requirements/` | Backlog HUs are formalized as requirements |
| `02-domain/` | Problem framing reveals domain entities like `RepairOrder` and `Vehicle` |
| `15-project-control/technical-backlog.md` | Technical debt identified during definition |

---

## Questions this section must answer

- What problem exactly are we solving? (Sudden mechanical breakdowns without reliable real-time dispatch)
- What does product success look like? (Response times under 30 minutes and 4.8-star satisfaction rating)
- What do we build first and why? (MVP core request flow and GPS tracking)
- What do we NOT build in this cycle? (Full vehicle insurance policy sales and heavy commercial fleet maintenance)