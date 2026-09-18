# Definition of Ready (DoR) - FIXGO

> A User Story is **Ready** when the entire team can start it in the next sprint
> without needing to resolve fundamental questions mid-sprint.
> If a story doesn't meet this DoR, it goes back to refinement.

---

## DoR checklist

Before moving a User Story to "Ready for Sprint", verify:

### Clarity
- [ ] The story is written in the format: **As [role], I want [action], so that [benefit]**
- [ ] The role is specific (e.g., "As an authenticated Client" or "As an available Mechanic").
- [ ] The expected benefit is clear and verifiable.

### Acceptance Criteria
- [ ] There are at least 2 acceptance criteria written in the **Given / When / Then** (Gherkin) format.
- [ ] The criteria cover the happy path AND the main error cases (e.g., GPS signal lost, Firebase connection error).
- [ ] The criteria are testable by the QA developer.
- [ ] There are no ambiguous criteria.

### Dependencies
- [ ] All external dependencies (Google Maps API, Firebase Cloud Messaging, MySQL integrations) are identified.
- [ ] Blocking dependencies are resolved OR a workaround is defined.
- [ ] If it depends on another story, that story is already Done or In Progress.

### Estimation
- [ ] The team has estimated the story using Planning Poker.
- [ ] There is agreement that the story fits in one 2-week sprint.
- [ ] If it is estimated at 8 or 13 points, it has been broken down into smaller stories.

### Technical readiness
- [ ] The necessary accesses and Firebase/MySQL environments are available.
- [ ] The API contracts are defined if the story involves new endpoints.
- [ ] There is a definition of the data model in `06-data/models.md` if there are database changes.
- [ ] The impact on other modules (e.g., matching algorithm) is identified.

### Non-functional requirements
- [ ] Performance and concurrency requirements are specified (e.g., response < 3 seconds).
- [ ] Security requirements are considered (JWT tokens, role validation).
- [ ] Error tracking and logging requirements are included.

---

## Common reasons a story is NOT ready

| Problem | What to do |
|---------|-----------|
| Unclear requirements | Schedule a short refinement session with the Project Leader. |
| Missing acceptance criteria | Add criteria before the next sprint planning. |
| Unknown dependencies | The team reviews and documents dependencies (e.g., Maps API quotas). |
| Too large (> 8 SP) | Break it down into smaller, testable stories. |
| No access to test environment | Generate credentials for Firebase/MySQL before the sprint. |
| Unclear data strategy | Review the Data Ownership Matrix in the models document. |

---

## DoR vs DoD

| | Definition of Ready (DoR) | Definition of Done (DoD) |
|-|--------------------------|--------------------------|
| **When** | Before starting the story | After finishing the story |
| **Who verifies** | Team in planning/refinement | Team in review |
| **Purpose** | Ensure the team can start without blockers | Ensure the increment is shippable |

---

## Correlations

- Full DoD → `00-governance/definition-of-done.md`
- User Stories backlog → GitHub Projects Board