# Technical Security Rules - FIXGO

> Mandatory technical controls that apply to all project code.
> These rules complement the security policy (`security-policy.md`) with
> concrete implementation practices for the FIXGO platform.

---

## OWASP Top 10 — Controls per category

### A01 — Broken Access Control

```java
// ❌ BAD — trusting frontend data
String userId = request.getParameter("userId");

// ✅ GOOD — extract from verified Firebase JWT token in the security context
String userId = securityContext.getAuthentication().getName(); 
```

**Rules:**
- Every protected endpoint MUST have the authentication middleware/filter applied.
- Permissions are verified in the Use Case / Service layer, not in the Controller.
- A resource is only returned if the user has `read` permission for it.
- Write actions require explicit `write` or `delete` permissions.

### A02 — Cryptographic Failures

**Rules:**
- **Passwords:** FIXGO delegates user password management to Firebase Auth. If any internal system password must be stored, use **bcrypt** with cost factor ≥ 12. Never MD5 or SHA-1.
- **Sensitive data in transit:** HTTPS mandatory in all environments except local.
- **Sensitive data at rest:** Encrypt with AES-256-GCM any fields marked as PII in MySQL.
- Never log passwords, tokens, or credit card data.

### A03 — Injection

**SQL:**
```java
// ❌ BAD — direct concatenation
String query = "SELECT * FROM vehicles WHERE license_plate = '" + userInput + "'";

// ✅ GOOD — parameterized query (JDBC)
String query = "SELECT * FROM vehicles WHERE license_plate = ?";
PreparedStatement pstmt = connection.prepareStatement(query);
pstmt.setString(1, userInput);

// ✅ GOOD — ORM (Hibernate/JPA)
Vehicle vehicle = vehicleRepository.findByLicensePlate(userInput);
```

**Rules:**
- Parameterized queries ALWAYS. Zero concatenated strings in SQL.
- Validate and sanitize all inputs with a validation library (e.g., `jakarta.validation`).

### A04 — Insecure Design

- Every User Story that exposes client or workshop data must undergo a privacy review.
- Bulk query endpoints have mandatory pagination (maximum 100 records per page).
- Do not expose sequential internal IDs in the API; use UUIDs.

### A05 — Security Misconfiguration

```text
# Verification checklist per environment
□ Stack traces NOT visible in production.
□ Security headers configured:
  - X-Content-Type-Options: nosniff
  - X-Frame-Options: DENY
  - Strict-Transport-Security in production
□ Unnecessary ports closed (e.g., MySQL 3306 blocked from public internet).
□ Development credentials NOT in production.
```

### A06 — Vulnerable Components

**Rules:**
- Run dependency vulnerability checks (e.g., OWASP Dependency-Check for Maven/Gradle) before each release.
- **Critical/High** vulnerabilities block the deploy.
- Renew dependencies each sprint (at least once).
- Do not use `latest` versions in dependencies; use exact versions.

### A07 — Identification and Authentication Failures

- Firebase JWT access tokens have a maximum expiration of **1 hour**.
- Rate limiting on sensitive endpoints: maximum 10 attempts per IP in 5 minutes.
- Account lockout after 5 consecutive failed login attempts (handled via Firebase settings).

### A08 — Software and Data Integrity Failures

- Verify Docker image checksums before using in production.
- Third-party webhooks (e.g., payment gateways) must verify cryptographic signatures.

### A09 — Security Logging and Monitoring Failures

- Every failed authentication must be logged with IP, timestamp, and user-agent.
- Log delete operations with who, when, and what was deleted (e.g., Workshop deleted by SuperAdmin).
- Security logs are retained for a minimum of **90 days**.
- Automatic alerts configured for:
  - More than 50 HTTP 401/403 errors in 5 minutes.

### A10 — Server-Side Request Forgery (SSRF)

- URLs constructed from user input MUST be validated against an allowlist of permitted domains.
- Do not fetch from private IPs (192.168.x.x, 10.x.x.x, 127.x.x.x) from the server.

---

## User input handling

```java
// Example with Jakarta Validation — always validate in the DTO layer
public class CreateWorkshopRequest {
    
    @NotBlank
    @Email
    @Size(max = 255)
    private String email;

    @NotBlank
    @Size(min = 1, max = 100)
    private String workshopName;
}
// The controller will automatically reject invalid payloads with HTTP 400
```

**Rule:** All external inputs pass through a validation schema before reaching the domain logic.

---

## Secure error handling

```java
// ❌ BAD — exposes internal details (Stack Trace)
return ResponseEntity.status(500).body(new ErrorResponse(e.getMessage(), e.getStackTrace()));

// ✅ GOOD — generic message + traceId for internal correlation
return ResponseEntity.status(500).body(new ErrorResponse(
    "INTERNAL_SERVER_ERROR", 
    "An unexpected error occurred", 
    MDC.get("traceId")
));
```

---

## Correlations

- Security policy (management, access, vault) → `00-governance/security-policy.md`
- Authentication and JWT → `07-api/authentication.md`
- Observability and security logs → `13-operations/observability.md`