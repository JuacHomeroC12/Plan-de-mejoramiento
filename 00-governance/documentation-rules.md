# Documentation Rules - FIXGO

> These rules determine how documentation is written, organized, and maintained in this project.
> Documentation that does not follow these rules may be rejected in code review.

---

## Core principle

> **"Documentation is code. If it's not up to date, it's broken."**

Every User Story (HU) that modifies system behavior MUST include updating the affected documents.
The DoD requires it.

---

## Language

| Artifact | Language |
|----------|----------|
| Source code (variables, functions, classes) | English |
| Code comments | English |
| Commits | English (Conventional Commits) |
| Branch names | English |
| Markdown documentation | Spanish (Matching the official SRS) |
| OpenAPI contracts (descriptions) | English |
| Error messages returned to frontend | Spanish (Localized for FIXGO users) |
| Internal system logs | English |

> **Rule:** Once the language for each category is chosen, it is binding for the entire project.
> Mixing languages in the same category is grounds for PR rejection.

---

## File structure

```text
Each section has its README.md that explains the folder's purpose.
Content documents use kebab-case.md (e.g.: domain-map.md, risk-register.md).
Templates are prefixed with _ to appear first (e.g.: _template-hu.md, _template-adr.md).
ADRs are numbered sequentially: ADR-001-short-title.md.