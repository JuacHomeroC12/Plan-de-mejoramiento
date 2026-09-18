# Problem Framing — Problem Definition — FIXGO

> **Why this document exists:** Before designing solutions, the team must be
> aligned on the problem it solves. This document captures that alignment.
> A well-defined problem is already halfway to a solution.

---

## 1. The problem in one sentence

**Vehicle owners stranded on highways or urban roads** who **experience sudden mechanical breakdowns far from a trusted workshop** struggle with **finding reliable, immediate roadside assistance and transparent pricing** because **traditional towing and mechanic services rely on fragmented phone calls, lack real-time location tracking, and offer unpredictable wait times**, resulting in **hours of stranded exposure, overpaid emergency rates, and severe stress**.

---

## 2. Affected users

| Segment | Description | Estimated size | Priority |
|---------|-------------|---------------|---------|
| Urban & Highway Drivers | Private vehicle owners who commute or travel and face unexpected mechanical failures. | High | High |
| Independent Mechanics | Local roadside technicians and mobile mechanics looking for direct service requests. | Medium | High |

### Jobs-to-be-done (JTBD)

**When** my car breaks down unexpectedly on the road,
**I want** to quickly request a trusted mechanic or towing service with real-time tracking and clear pricing,
**so that** I can resume my journey safely without being overcharged.

---

## 3. Evidence of the problem

| Evidence type | Source | Date | Key finding |
|--------------|--------|------|------------|
| User interviews | 15 interviews with local drivers in Neiva | 2026-04 | 80% reported waiting over 2 hours for a tow truck without knowing its exact location. |
| Support data | Local driver survey feedback | 2026-05 | Overcharged emergency fees are the primary complaint when seeking roadside help. |
| Benchmarking | Market analysis of current towing services | 2026-06 | Lack of digital platforms connecting drivers directly to nearby mobile mechanics. |
| Direct observation | Field research on urban highways | 2026-07 | Drivers struggle to share accurate geographic coordinates when stranded. |

---

## 4. Current user solution (and its problems)

| Current solution | Limitations | Cost/Friction |
|-----------------|------------|--------------|
| Yellow pages / Phone directories | Outdated contacts, no availability guarantee | 1 to 3 hours of calling |
| Informal WhatsApp recommendations | Unreliable response times, no official guarantee | High uncertainty and variable pricing |
| Traditional insurance towing | Long bureaucratic dispatch processes | 2 to 4 hours average wait time |

---

## 5. Solution hypothesis

**We believe that** a real-time mobile platform connecting drivers with nearby verified mechanics using GPS tracking and transparent service orders
**for** stranded vehicle owners,
**will achieve** a reduction of response times to under 30 minutes and complete pricing transparency.
**We will know we succeeded when** 70% of requested assistance orders are successfully completed through the platform with an average rating above 4.5 stars.

---

## 6. Success metrics (North Star)

| Metric | Current baseline | 6-month target | How to measure it |
|--------|----------------|---------------|-------------------|
| Average response time | 120 minutes | 30 minutes | System timestamp logs from request to mechanic arrival |
| Completed service rate | 40% | 85% | Database transaction logs of finished orders |

**North Star Metric:** Successful roadside assistance requests completed per week with a response time under 30 minutes.

---

## 7. Hypothesis risks

| Risk | Probability | Impact | Experiment to validate |
|------|------------|--------|----------------------|
| Mechanics will not adopt the mobile app | Medium | High | Pilot onboarding program with local independent mechanics in Neiva |
| Users lack internet connection while stranded on remote highways | Medium | High | Lightweight progressive web app design optimized for low bandwidth |

---

## 8. Out of scope (we do not solve)

- Full vehicle insurance policy management and sales: We focus strictly on immediate emergency assistance dispatch, not long-term insurance policies.
- Heavy industrial machinery and commercial fleet depot maintenance: FIXGO is built for consumer and light commercial road vehicles.

---

## Correlations

- Product vision → `03-product/vision.md`
- HUs that implement this solution → `04-requirements/user-stories.md`
- Detailed KPIs → `13-operations/README.md`