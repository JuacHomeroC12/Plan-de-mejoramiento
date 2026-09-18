# Definition of Done (DoD) - FIXGO

A User Story is considered **DONE** when it meets ALL criteria on this checklist.
If even one is missing, the story is NOT done and must return to the "In Progress" status.

## Mandatory checklist

### Code
- [ ] Code implements all acceptance criteria of the user story.
- [ ] Code was reviewed and approved by at least 1 team member (Pull Request review).
- [ ] Code follows project standards (linting and formatting pass).
- [ ] No technical debt introduced without registering it in the technical backlog.

### Tests
- [ ] Unit tests written for new business logic.
- [ ] Test coverage does not decrease from the project baseline.
- [ ] All tests pass locally and in CI.
- [ ] Acceptance criteria verified manually by QA or via automated tests.

### Integration
- [ ] Changes do not break other services (integration tests pass).
- [ ] If API changes: OpenAPI/Swagger contract updated.
- [ ] If data model changes: `06-data/models.md` updated, respecting the Data Ownership Matrix.
- [ ] If new/modified events (e.g., Firebase Cloud Messaging): event documentation updated.

### Deployment
- [ ] Code is mergeable to `main` (no conflicts).
- [ ] CI/CD pipeline is green on the branch.
- [ ] Deployed to staging/testing environment.
- [ ] Basic smoke test passing on staging.

### Documentation
- [ ] README.md or SRS updated if the public interface or logic changed.
- [ ] If a significant technical decision was made: ADR created or updated in `05-architecture/`.

---

## Allowed exceptions

The following exceptions must be explicitly agreed to by the Project Leader or the Tech Lead:
- E2E tests omitted due to environment limitations (document the risk).
- Documentation deferred for urgent delivery (create a tech-debt ticket).

---

## What is NOT a Done criterion

- "The code is on my machine" — it must be pushed to the GitHub repository.
- "It works on my local environment" — it must work on the staging environment.
- "The PM/PO approved it" — that is the product Definition of Done, not the technical code's DoD.