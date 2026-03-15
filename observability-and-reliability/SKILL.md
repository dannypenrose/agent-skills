---
name: observability-and-reliability
description: >
  Enforce observability and reliability standards when implementing logging, metrics, tracing,
  error handling, alerting, or SRE practices. Use this skill whenever the user adds logging
  statements, configures monitoring, sets up health checks, implements circuit breakers, defines
  SLOs/SLIs, creates alerting rules, works on error budgets, or discusses incident response.
  Also triggers when the user works on retry logic, backoff strategies, graceful degradation,
  or chaos engineering — even if they don't explicitly mention 'observability' or 'reliability'.
trigger: auto
---

# Observability & Reliability Standards

## Context Detection

Before providing guidance, detect what the user is working on and fetch ONLY the relevant standard using WebFetch:

| User Activity | Standard to Read |
|---|---|
| Adding logging, metrics, tracing, dashboards | `https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/reliability/observability-standards.md` |
| Defining SLOs, SLIs, error budgets, alerting thresholds | `https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/reliability/error-budgets.md` |
| Incident response, runbooks, postmortems, on-call | `https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/reliability/incident-management.md` |
| Resilience testing, fault injection, failure scenarios | `https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/reliability/chaos-engineering.md` |
| Backup strategies, disaster recovery, restore procedures | `https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/reliability/backup-disaster-recovery.md` |

**Do NOT read all five documents.** Read only the one(s) directly relevant to the current task, then follow the standards found there.

## Behavioral Rules

### 1. Structured Logging

All logging MUST follow structured patterns:

- Use **structured JSON logging** in production (not `console.log` with string interpolation).
- Every log entry must include: `timestamp`, `level`, `message`, `service`, `correlationId`.
- Use correlation IDs (trace IDs) propagated through the full request lifecycle.
- Use appropriate log levels:

| Level | When to Use |
|-------|-------------|
| `error` | Unexpected failures requiring attention (5xx, unhandled exceptions, data corruption) |
| `warn` | Recoverable issues, degraded state, approaching thresholds (rate limits, retries) |
| `info` | Significant business events, request lifecycle (start/end), state transitions |
| `debug` | Diagnostic detail for development (query params, intermediate values) |

- **NEVER log**: passwords, tokens, API keys, session IDs, PII (emails, phone numbers, SSNs, IP addresses in GDPR contexts), credit card numbers, health data, or any secret material.
- Mask or redact sensitive fields if they must appear in logs (e.g., `email: "d***@example.com"`).

### 2. Error Handling

- Categorize errors: `operational` (expected, recoverable) vs `programmer` (bugs, crash-worthy).
- Operational errors get retries, fallbacks, and graceful degradation.
- Programmer errors get logged at `error` level with full stack traces and re-thrown.
- Every caught error must produce a log entry with: error class/name, message, stack trace, and correlation ID.
- Use error codes or categories for alerting (not string matching on messages).

### 3. Health Check Pattern

Every service exposes:

```
GET /health          -> { status: "ok", timestamp, version }
GET /health/ready    -> checks downstream dependencies (DB, cache, queues)
GET /health/live     -> simple liveness (process is running)
```

- `/health/ready` returns 503 if any critical dependency is down.
- `/health/live` always returns 200 unless the process is deadlocked/crashed.
- Health checks must NOT require authentication.

### 4. Metrics & Tracing

- Use RED metrics for services: **R**ate, **E**rrors, **D**uration.
- Use USE metrics for resources: **U**tilization, **S**aturation, **E**rrors.
- Trace IDs must propagate across service boundaries (HTTP headers, message metadata).
- Set up alerts on SLO burn rate, not raw error counts.

### 5. Resilience Patterns

When implementing retry logic, circuit breakers, or fallbacks:

- Retries: use exponential backoff with jitter. Cap max retries (typically 3).
- Circuit breakers: define failure threshold, recovery timeout, and half-open probe count.
- Timeouts: every external call must have an explicit timeout. No unbounded waits.
- Graceful degradation: return cached/stale data or a reduced feature set rather than a full failure.

## Checklist

Before completing any observability/reliability task, verify:

- [ ] Structured logging with correct levels (no `console.log` in production code)
- [ ] No PII, secrets, or tokens in log output
- [ ] Correlation IDs present and propagated
- [ ] Errors categorized and logged with stack traces
- [ ] Health check endpoints implemented (if adding a new service)
- [ ] Timeouts set on all external calls
- [ ] Retry logic uses exponential backoff with jitter
- [ ] Alerts based on SLO burn rate, not raw thresholds
