---
name: security-review
description: "Enforce security standards when writing or reviewing code that handles authentication, authorization, user input, secrets, encryption, or any security-sensitive operations. Use this skill whenever the user works on login flows, password handling, JWT tokens, session management, API keys, CORS, CSP headers, rate limiting, file uploads, SQL queries, environment variables with secrets, or infrastructure configuration. Also triggers for code reviews that mention security, OWASP compliance, or vulnerability fixes — even if the user doesn't explicitly ask for a 'security review'."
---

# Security Review

Enforce security standards from the organization's authoritative governance documents whenever writing or reviewing security-sensitive code.

## When This Skill Activates

This skill applies whenever work touches:

- Authentication (login, logout, password reset, MFA, OAuth)
- Authorization (guards, roles, permissions, ownership checks)
- JWT tokens (creation, validation, refresh, storage)
- Session management (cookies, session IDs, expiry)
- User input handling (forms, query params, file uploads, API bodies)
- Secrets (API keys, env vars, credentials, encryption keys)
- Encryption (hashing, TLS, field-level encryption)
- Security headers (CSP, HSTS, CORS, X-Frame-Options)
- Rate limiting and brute-force protection
- SQL queries (raw queries, ORM usage)
- Infrastructure (SSH, firewalls, TLS config, server hardening)
- PII or sensitive data handling
- Dependency updates or additions
- Code reviews mentioning security, OWASP, or vulnerabilities

## Step 1: Read the Applicable Standards

**Always fetch** the primary security guidelines using WebFetch before proceeding:

```
https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/governance/security-guidelines.md
```

**If the work involves infrastructure** (SSH, TLS, firewall, server config, Docker, deployment), also read:

```
https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/governance/infrastructure-security.md
```

**If the work involves PII or sensitive user data** (emails, names, addresses, health data, financial data, consent, data export/deletion), also read:

```
https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/governance/data-governance.md
```

**If the work involves self-hosted infrastructure** (VPS, bare-metal, self-managed servers, reverse proxies, DNS, SSL certificates), also read:

```
https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/governance/self-hosting-standards.md
```

## Step 2: Apply Security Checks

### OWASP Top 10 Verification

Check code against these vulnerability categories:

| # | Vulnerability | What to Look For |
|---|---------------|-----------------|
| A01 | Broken Access Control | Missing auth guards, no ownership checks, IDOR, missing role verification |
| A02 | Cryptographic Failures | Weak hashing, missing TLS, plaintext secrets, weak JWT algorithms |
| A03 | Injection | Raw SQL with string interpolation, unsanitized HTML, `$queryRawUnsafe` |
| A04 | Insecure Design | Missing threat model, no rate limiting on sensitive endpoints |
| A05 | Security Misconfiguration | Missing security headers, overly permissive CORS, debug mode in prod |
| A06 | Vulnerable Components | Outdated deps with known CVEs, unaudited packages |
| A07 | Auth Failures | No rate limiting on login, weak password policy, long-lived tokens |
| A08 | Integrity Failures | Missing lockfile integrity, unsigned artifacts |
| A09 | Logging Failures | Secrets in logs, missing audit trail for auth events |
| A10 | SSRF | Unvalidated URLs, no allowlists for external requests |

### Quick-Reference Checklist

Use this for fast checks without reading the full standard every time:

#### Authentication
- [ ] Passwords hashed with bcrypt (cost 12+) or argon2id
- [ ] JWT access tokens expire in 15 minutes or less
- [ ] JWT refresh tokens expire in 7 days or less
- [ ] JWT uses RS256 for distributed systems, HS256 acceptable for single-service
- [ ] Never store passwords, secrets, or sensitive PII in JWT payload
- [ ] Session ID regenerated on auth state change (login, privilege escalation)
- [ ] Cookies use `Secure`, `HttpOnly`, `SameSite=Strict` flags
- [ ] Multi-app environments use unique cookie names per app (NextAuth prefix pattern)
- [ ] Rate limiting on login endpoint (max 5 attempts per 15 min)
- [ ] Account lockout or progressive delay after failed attempts
- [ ] Use `req.user.sub` (not `req.user.id`) in NestJS auth context

#### Input Validation
- [ ] All input validated server-side (never trust client-only validation)
- [ ] Use class-validator DTOs in NestJS or Zod schemas
- [ ] SQL: use Prisma ORM or parameterized queries -- never string interpolation
- [ ] Never use `$queryRawUnsafe` with user input
- [ ] HTML output escaped by default (React handles this); `dangerouslySetInnerHTML` only with sanitized content
- [ ] File uploads validated: type, size, and content (not just extension)
- [ ] Email and string fields trimmed and length-limited

#### Secrets Management
- [ ] No hardcoded secrets in source code
- [ ] Secrets in `.env` files that are gitignored
- [ ] `.env.example` committed (with empty values) for documentation
- [ ] `NEXT_PUBLIC_*` vars contain NO secrets (they are bundled into client JS)
- [ ] Use `requireEnv()` pattern to fail fast on missing secrets
- [ ] Different secrets per environment (dev/staging/prod)
- [ ] Secrets never logged -- sanitize log output
- [ ] Server-side env files have `chmod 600` permissions

#### Security Headers
- [ ] `Strict-Transport-Security: max-age=31536000; includeSubDomains`
- [ ] `Content-Security-Policy` configured (at minimum `default-src 'self'`)
- [ ] `X-Content-Type-Options: nosniff`
- [ ] `X-Frame-Options: DENY`
- [ ] `Referrer-Policy: strict-origin-when-cross-origin`
- [ ] Use `helmet()` middleware in NestJS/Express

#### CORS
- [ ] Explicitly list allowed origins -- never use `*` in production
- [ ] Credentials mode matches cookie/auth strategy
- [ ] Preflight caching configured

#### Rate Limiting
- [ ] General API: 100 requests per 15 min window
- [ ] Auth endpoints: 5 attempts per 15 min (skip successful requests)
- [ ] Use `express-rate-limit` or NestJS `@nestjs/throttler`

#### Logging
- [ ] Log: login success/failure, logout, password changes, permission denied, rate limit exceeded
- [ ] Never log: passwords, tokens, session IDs, API keys, credit cards, SSNs
- [ ] Include `userId` and `ip` in security event logs

#### Dependencies
- [ ] Run `pnpm audit` before adding new dependencies
- [ ] Critical CVEs patched within 24 hours
- [ ] High CVEs patched within 7 days

#### Tauri Desktop (if applicable)
- [ ] All security logic in Rust, not JavaScript
- [ ] License validation in compiled Rust only
- [ ] OAuth tokens exchanged server-side (in Rust)
- [ ] Tokens in encrypted store, never localStorage
- [ ] Least-privilege capability permissions

#### PII / Data Privacy (if applicable)
- [ ] PII classified by sensitivity level
- [ ] PII encrypted at rest
- [ ] Explicit consent collected before processing PII
- [ ] Data retention policies enforced (automated cleanup)
- [ ] Data export and deletion capabilities exist
- [ ] Anonymization used for analytics data
- [ ] Never log PII fields (email in audit context is acceptable with userId)

## Step 3: Flag Violations

When violations are found:

1. **State the violation clearly** with the specific OWASP category or standard section
2. **Quote the relevant rule** from the standard
3. **Show the fix** with a code example
4. **Assess severity**: Critical (exploitable now), High (exploitable with effort), Medium (defense-in-depth gap), Low (best practice improvement)

Example format:

```
SECURITY: A03 Injection - SQL injection risk
Standard: "ALWAYS use parameterized queries" (Security Guidelines > Input Validation)
Severity: Critical

Found: prisma.$queryRawUnsafe(`SELECT * FROM users WHERE id = '${userId}'`)
Fix:   prisma.$queryRaw`SELECT * FROM users WHERE id = ${userId}`
```

## Step 4: Proactive Warnings

Do not wait for a review request. When writing code alongside the user, proactively warn about:

- Missing auth guards on new endpoints
- Secrets that might accidentally be committed
- Missing input validation on new DTOs
- Endpoints without rate limiting
- New dependencies that haven't been audited
- PII being logged or exposed in API responses
- Missing security headers on new routes
- Overly permissive CORS configurations
