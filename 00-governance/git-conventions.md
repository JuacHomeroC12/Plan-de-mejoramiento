# Git Conventions - FIXGO

> **Read this document before making your first commit on the project.**

## Branch strategy

```text
main        ← Production / Stable. Merge from release only.
  └── dev   ← Continuous integration. Merge from features.
        └── feat/[description]    ← One branch per feature/user story
        └── fix/[description]     ← One branch per bugfix
        └── chore/[description]   ← Infrastructure, docs, dependency changes
        └── hotfix/[description]  ← Urgent fixes directly to main
```

**Rules:**
- Nobody commits directly to `main` or `dev`.
- Every task = one branch + one Pull Request.
- One branch = one task (do not mix different features).
- Branches are deleted after merge.

---

## Branch naming format

```text
[type]/[description-in-kebab-case]

Examples:
feat/vehicle-registration
fix/gps-coordinate-overlap
chore/initial-docs
hotfix/auth-token-expiration
```

---

## Commit format (Conventional Commits)

```text
[type]([scope]): [lowercase description, imperative mood, no trailing period]

[optional body — explain WHY, not what]

[optional footer — issue/user story references]
```

**Types:**
| Type | When to use |
|------|-------------|
| `feat` | New functionality |
| `fix` | Bug fix |
| `docs` | Documentation only |
| `style` | Formatting, whitespace (no logic change) |
| `refactor` | Code refactoring without behavior change |
| `test` | Add or modify tests |
| `chore` | Tooling, dependencies, CI, documentation structure |
| `perf` | Performance improvement |

**Examples:**
```text
feat(auth): implement firebase user registration

fix(vehicles): correct license plate validation format
Closes #12

docs(srs): update technical requirements specifications

chore(docs): cleanup initial documentation placeholders
```

---

## Pull Request policy

- **Size:** maximum 400 lines of code (excluding tests). If larger, split it.
- **Reviewers:** minimum 1 approval before merging.
- **Review time:** reviewer has a maximum of 24 business hours.
- **Template:** use the template at `.github/pull_request_template.md`.
- **Green CI:** merge only proceeds if all pipeline checks pass.

---

## Merge policy

- Use **Squash and Merge** for features (keeps `dev` history clean).
- Use **Merge Commit** for releases to `main` (preserves full history).
- **Do not** use Rebase & Merge (creates confusion in shared history).

---

## Tags and versioning

Follow [SemVer](https://semver.org/): `MAJOR.MINOR.PATCH`

```bash
# When releasing to production
git tag -a v1.0.0 -m "Release v1.0.0: initial MVP launch of FIXGO"
git push origin v1.0.0
```