---
name: deployment-and-infrastructure
description: "Enforce deployment, CI/CD, containerization, and infrastructure standards when working on deployment configurations, Dockerfiles, docker-compose files, GitHub Actions workflows, deployment scripts, environment files, reverse proxy configs, or server setup. Use this skill whenever the user creates or modifies Dockerfiles, CI pipelines, .env files, nginx/caddy configs, or discusses deployment strategy, blue-green deployments, container orchestration, rollback procedures, or self-hosting — even if they don't explicitly mention 'deployment standards'."
---

# Deployment & Infrastructure Standards

You are enforcing authoritative deployment, CI/CD, containerization, environment management, and self-hosting standards. When this skill activates, follow the context detection rules below to load only the relevant standards, then apply them to the work at hand.

## Context Detection & Standard Loading

Detect the type of file being edited and read ONLY the 1-2 relevant standards documents. Never read all six at once.

| Files Being Edited | Standards to Read |
|---|---|
| `Dockerfile`, `docker-compose*.yml`, `.dockerignore` | `https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/development/containerization.md` |
| `.github/workflows/*.yml`, CI pipeline configs | `https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/development/ci-cd-pipelines.md` |
| `.env*` files, environment configs, secret management | `https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/development/environment-management.md` |
| Deploy scripts, systemd units, rsync configs, release workflows | `https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/development/deployment-standards.md` |
| Caddy/Nginx configs, server setup, DNS, SSL, systemd services | `https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/governance/self-hosting-standards.md` |
| Public repo setup, LICENSE, open-source CI | `https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/governance/open-source.md` |
| Git hooks, Husky, lint-staged configs | `https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/development/git-hooks-husky.md` |
| Local dev setup, dev scripts, workspace config | `https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/development/local-development.md` |
| VS Code workspace tasks, `.code-workspace` files | `https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/development/workspace-tasks.md` |

**Procedure:**
1. Identify which files are being created or modified.
2. Map them to the table above.
3. Use the WebFetch tool to load the 1-2 matching standards documents.
4. Apply the rules from those documents to the current task.
5. Use the quick-reference rules below as an immediate checklist without needing to re-read the full documents.

## Quick-Reference Rules

These are the most commonly needed checks, extracted from the full standards. Apply them immediately; consult the full standard document for edge cases.

### Dockerfile Checks

- [ ] Multi-stage build (separate build and runtime stages)
- [ ] Base image version pinned (`node:22-alpine`, never `node:latest` or `node:alpine`)
- [ ] Non-root user created and set via `USER` before `ENTRYPOINT`/`CMD`
- [ ] `HEALTHCHECK` instruction defined
- [ ] `.dockerignore` exists and excludes `.env`, `.env.*`, `.git`, `node_modules`, `dist`
- [ ] No secrets baked into image (no `ENV` with credentials, no `COPY .env`)
- [ ] Layers ordered by change frequency (system packages -> lockfile+install -> app code)
- [ ] JSON notation for `ENTRYPOINT`/`CMD` (exec form, not shell form)
- [ ] `--frozen-lockfile` used for package installs
- [ ] Combined `RUN` commands to reduce layers; clean up caches in same layer

### Docker Compose Checks

- [ ] Production: `read_only: true`, `security_opt: [no-new-privileges:true]`
- [ ] Resource limits defined (`deploy.resources.limits`)
- [ ] Health checks on every service
- [ ] `restart: unless-stopped` for production services
- [ ] Logging configured with size limits (`max-size`, `max-file`)
- [ ] Ports bound to `127.0.0.1` unless public access required
- [ ] Image tags pinned to version (never `latest` in production)

### CI/CD Pipeline Checks (GitHub Actions)

- [ ] **OneRunner pattern**: Single job for build + deploy (no artifact upload/download between jobs)
- [ ] `paths:` trigger scoped to relevant directories
- [ ] `workflow_dispatch:` present for manual deploys
- [ ] `concurrency:` group with `cancel-in-progress: true`
- [ ] Runtime version as workflow-level `env:` variable (not hardcoded)
- [ ] Dependency caching configured (`setup-node` cache or `actions/cache`)
- [ ] rsync uses standard flags: `-rz --delete --no-times --no-perms --no-owner --no-group --omit-dir-times`
- [ ] SSH keepalive: `-o ServerAliveInterval=30` on long-running SSH commands
- [ ] `SERVER_USER` is app-specific deploy user (not `ops` or `root`)
- [ ] No lint/test/type-check steps in deploy workflows (these belong in pre-push hooks)
- [ ] Monorepo backends using `pnpm deploy`: add `-L` flag to rsync for symlink dereferencing
- [ ] `environment:` set if secrets are scoped to a GitHub environment

### Environment File Checks

- [ ] `.env` and `.env*.local` are gitignored
- [ ] `.env.production` is committed (contains only public `NEXT_PUBLIC_*` vars, no secrets)
- [ ] No secrets in committed files or build args
- [ ] Variable naming: `UPPER_SNAKE_CASE`, descriptive (e.g., `DATABASE_URL` not `DB_URL`)
- [ ] Server env files at `/etc/{app_name}.env` with permissions `chmod 600`
- [ ] Startup validation with Zod or equivalent (app fails fast on missing/invalid config)
- [ ] `NEXT_PUBLIC_*` vars go in `frontend/.env.production`, not injected via CI `env:` blocks
- [ ] Backend feature flags use `ENABLE_*` format
- [ ] Frontend feature flags use `NEXT_PUBLIC_FEATURE_FLAG_*` format

### Deployment Strategy Checks

- [ ] Zero-downtime deployment pattern selected (rolling, blue-green, or canary)
- [ ] Rollback procedure documented and tested (< 5 min target)
- [ ] Health check endpoints (`/health` for liveness, `/ready` for readiness)
- [ ] Graceful shutdown handles `SIGTERM` (drain connections, close DB, close cache)
- [ ] Smoke tests run post-deployment
- [ ] Feature flags configured for new features (toggle without redeploy)
- [ ] Immutable image tags for production (semver or SHA-based, never `latest`)

### Self-Hosting / Server Checks

- [ ] Caddy/Nginx configured with security headers (HSTS, X-Content-Type-Options, X-Frame-Options)
- [ ] SSL/TLS via Let's Encrypt with automatic renewal
- [ ] systemd service: `Restart=always`, `RestartSec=10`, `NoNewPrivileges=true`, `ProtectSystem=strict`
- [ ] `EnvironmentFile=/etc/{app_name}.env` for secrets (not inline `Environment=`)
- [ ] Firewall rules configured (only expose necessary ports)
- [ ] Automated security updates enabled (`unattended-upgrades`)
- [ ] Backup script with offsite storage; tested restore procedure
- [ ] Log rotation configured

## Applying Standards

When reviewing or creating deployment-related files:

1. **Read the relevant standard(s)** using the context detection table above.
2. **Check against the quick-reference rules** for the file type being edited.
3. **Flag violations** clearly, citing the specific rule.
4. **Provide the corrected code** following the patterns from the standard.
5. **Do not over-engineer** -- apply only what is relevant to the current scope. A simple single-server deploy does not need Kubernetes patterns.

## Common Patterns to Enforce

### The Forge Monorepo Specifics

When working on The Forge monorepo deployment:

- Frontend apps use Next.js standalone output
- Backend apps use NestJS
- `pnpm deploy --prod` for backend bundles; rsync with `-L` flag to follow workspace symlinks
- Shared packages need `"files": ["dist"]` in `package.json` to include built output in deploy bundles
- After removing large packages from deploy bundle, run `find . -xtype l -delete` to prune dangling symlinks
- Environment file convention follows the three-file pattern: `.env.local` (dev), `.env.production` (public, committed), `.env` (production secrets, gitignored)
- Port assignments are fixed per app (see project CLAUDE.md)

### Image Tagging for Production

```
ghcr.io/{org}/{app}:{semver}          # e.g., 1.2.3
ghcr.io/{org}/{app}:{semver}-{sha}    # e.g., 1.2.3-abc1234
ghcr.io/{org}/{app}:sha-{sha}         # e.g., sha-abc1234
```

Never deploy `latest` or branch tags to production.
