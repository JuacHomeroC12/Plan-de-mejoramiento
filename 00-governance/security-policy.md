# Security Policy - FIXGO

> Security is not a feature — it is a system property built from day one. This document
> defines the mandatory practices for the FIXGO platform.
> Any deviation must be explicitly approved by the Tech Lead / Project Leader.

---

## Security principles

1. **Defense in Depth:** Multiple security layers. If one fails, the others contain the damage.
2. **Least Privilege:** Each component has only the minimum necessary permissions.
3. **Fail Secure:** In case of error, the system denies access, does not allow it.
4. **Security by Design:** Security controls are designed from the start, not added at the end.
5. **Zero Trust:** Always verify, never implicitly trust, even within the internal network.

---

## Authentication (Firebase Auth)

FIXGO uses **Firebase Authentication** as the primary Identity Provider (IdP). Backend services do not manage passwords directly.

### JWT (Firebase ID Tokens)

| Property | Required value |
|----------|---------------|
| Token Provider | Google / Firebase Auth |
| Access token expiration | 1 hour (Managed by Firebase) |
| Required claims | `sub` (userId), `iat`, `exp` |
| Backend Validation | Must use Firebase Admin SDK to verify signature and expiration |

**Prohibited in the custom claims payload:**
- Passwords (never stored in our MySQL DB, handled by Firebase)
- Credit Card data
- Full PII (only the user ID and basic roles)

---

## Authorization

### RBAC (Role-Based Access Control)

Given FIXGO's domain (mechanical workshops), the system implements the following core roles:

| Role | Description |
|------|-------------|
| `SUPER_ADMIN` | FIXGO Platform Administrator (System owner) |
| `WORKSHOP_ADMIN` | Workshop owner or manager. Full access to their workshop's data. |
| `MECHANIC` | Workshop employee. Can update service statuses and view assigned vehicles. |
| `CLIENT` | Vehicle owner. Read-only access to their own service history and appointments. |

**Permission model:**

```text
Permission: [resource]:[action]

Examples:
  appointments:create
  vehicles:read
  services:update
  workshops:delete
```

**Validation:**
- Roles are attached as Firebase Custom Claims or queried from the MySQL User table.
- Each service validates the role permissions for the specific operation.

---

## Secure communication

### Transmission

- **HTTPS mandatory** in all environments except local.
- TLS 1.2 minimum; TLS 1.3 recommended.
- No direct database access from the internet (MySQL must be in a private subnet or restricted via firewall).

---

## Secret management

```text
✗ NEVER in source code
✗ NEVER in committed .env
✗ NEVER in logs
✗ NEVER in client error messages
✓ Environment variables
✓ Vault / Cloud Secret Managers
```

**Required Secrets for FIXGO:**
- Firebase Admin SDK Service Account JSON.
- MySQL Database connection strings and passwords.
- Push Notifications (FCM) server keys.

---

## Input validation and sanitization

### General rules

1. **Never trust user input.** Validate at the controller before processing.
2. **Whitelist, not blacklist.** Define what is allowed, not only what is prohibited.
3. **Reject early.** If input is invalid, respond HTTP 400 and do not process further.

### SQL Injection — Prevention (Java / MySQL)

```java
// ✗ VULNERABLE - String concatenation
String query = "SELECT * FROM users WHERE email = '" + userInput + "'";
Statement stmt = connection.createStatement();
ResultSet rs = stmt.executeQuery(query);

// ✓ SAFE — always use PreparedStatement
String query = "SELECT * FROM users WHERE email = ?";
PreparedStatement pstmt = connection.prepareStatement(query);
pstmt.setString(1, userInput);
ResultSet rs = pstmt.executeQuery();
```

### XSS — Prevention

```javascript
// ✗ VULNERABLE — rendering HTML without escaping
element.innerHTML = userProvidedContent;

// ✓ SAFE — use textContent or sanitize
element.textContent = userProvidedContent;
```

---

## OWASP Top 10 — Review checklist

| Vulnerability | Implemented control |
|---------------|-------------------|
| A01: Broken Access Control | RBAC (Mechanic vs Client) validation in each service |
| A02: Cryptographic Failures | TLS 1.2+, Firebase handles user passwords securely |
| A03: Injection | Prepared statements in MySQL, input validation |
| A04: Insecure Design | Threat modeling for workshop data privacy |
| A05: Security Misconfiguration | Review of default ports (e.g., MySQL 3306 restricted) |
| A06: Vulnerable Components | Dependabot for automatic dependency updates |
| A07: Authentication Failures | Delegated to Firebase Auth (brute-force protection built-in) |
| A08: Software Integrity Failures | Verify dependency checksums |
| A09: Logging Failures | Logs without PII, centralized |
| A10: SSRF | Whitelist of external URLs |

---

## Audit and security logs

### Events that are ALWAYS recorded

```text
Security events — store with retention > 1 year:
  auth.login.success
  auth.login.failure
  auth.unauthorized_access_attempt
  data.pii.accessed
  admin.role.changed
  workshop.mechanic.removed
```

**Required fields in security logs:**
- `userId` (or `ANONYMOUS` if not authenticated)
- `sourceIp`
- `action`
- `resource`
- `result` (SUCCESS / FAILURE)
- `timestamp`

---

## Vulnerability process

### What to do if you find a vulnerability

1. **Do not commit it to the public repo** or discuss it in open channels.
2. Immediately notify the Project Leader via a private channel.
3. Create a private issue.
4. Remediated in the current sprint if critical, in the next sprint if high.