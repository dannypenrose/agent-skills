# Agent Skills

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

A collection of 8 agent skills that automatically enforce engineering standards during AI-assisted development. Each skill detects the tech stack being worked on, fetches the relevant standard, and applies it silently as code is written or reviewed.

These skills are designed for use with any agentic coding tool that supports custom instructions or skill-like mechanisms -- including Claude Code, Codex, Gemini, Cursor, Windsurf, and others. They reference the [Engineering Standards](https://github.com/dannypenrose/engineering-standards) repository as their source of truth.

## Skills

| Skill | Triggers When | What It Enforces |
|-------|--------------|-----------------|
| [api-standards](./api-standards) | Working on endpoints, controllers, routes, DTOs, middleware, or API surfaces | REST API design patterns for NestJS, ASP.NET Core, and FastAPI -- including error responses, pagination, naming, status codes, versioning, and caching |
| [coding-standards](./coding-standards) | Writing, creating, implementing, or refactoring code in any supported language | Language-specific conventions for TypeScript (monorepo and standalone), .NET/C#, and Python -- covering naming, file structure, architecture, and imports |
| [database-and-migrations](./database-and-migrations) | Working with schemas, queries, migrations, ORMs, indexes, or seed data | Database naming conventions, required columns, index patterns, migration safety rules, N+1 prevention, and data migration best practices |
| [deployment-and-infrastructure](./deployment-and-infrastructure) | Editing Dockerfiles, CI pipelines, environment files, deploy scripts, or server configs | Containerisation, CI/CD pipelines (GitHub Actions), environment management, deployment strategy, and self-hosting/reverse proxy standards |
| [observability-and-reliability](./observability-and-reliability) | Adding logging, metrics, tracing, health checks, circuit breakers, SLOs, or alerting | Structured logging, error categorisation, health check patterns, RED/USE metrics, resilience patterns (retries, circuit breakers, timeouts) |
| [platform-specific-standards](./platform-specific-standards) | Developing for mobile (Expo), desktop (Tauri), Chrome extensions, WebSocket/SSE, i18n, feature flags, or accessibility | Platform-appropriate patterns with automatic detection from project signals like `app.config.ts`, `tauri.conf.json`, `manifest.json`, etc. |
| [security-review](./security-review) | Touching authentication, authorisation, user input, secrets, encryption, headers, or rate limiting | OWASP Top 10 compliance, JWT best practices, input validation, secrets management, security headers, CORS, and dependency auditing |
| [testing-and-quality](./testing-and-quality) | Writing or modifying tests, discussing testing strategy, or working on test files | Testing pyramid enforcement, coverage targets, AAA pattern, naming conventions, and framework-specific guidance for Jest, React Testing Library, and Vitest |

## How They Work

Each skill follows the same pattern:

1. **Detect** -- Identify the tech stack from project markers (e.g. `turbo.json` = monorepo, `*.csproj` = .NET)
2. **Fetch** -- Load the relevant standard document from the [Engineering Standards](https://github.com/dannypenrose/engineering-standards) repository
3. **Apply** -- Enforce the standard silently during development, flagging violations only when necessary
4. **Cite** -- When pushing back on an approach, reference the specific rule and explain why

Skills are designed to be **practical, not pedantic** -- they apply standards without lecturing and only speak up when a structural or architectural choice conflicts with the standard.

## Installation

### Claude Code

Copy any skill directory into your Claude Code skills folder:

```bash
# Install a single skill
cp -r api-standards ~/.claude/skills/

# Install all skills
cp -r */ ~/.claude/skills/
```

Or clone the entire repository and symlink:

```bash
git clone https://github.com/dannypenrose/agent-skills.git ~/.claude/agent-skills

# Symlink individual skills
ln -s ~/.claude/agent-skills/api-standards ~/.claude/skills/api-standards
ln -s ~/.claude/agent-skills/coding-standards ~/.claude/skills/coding-standards
# ... etc
```

Skills activate automatically when Claude Code detects matching context -- no manual invocation needed.

### Other Agents

For agents that support custom instructions (Codex, Cursor, Windsurf, etc.), copy the content of the relevant `SKILL.md` file into your agent's instruction configuration. The detection logic and standard references are written in plain markdown and are not tied to any specific platform.

## Supported Stacks

| Stack | Coding | API Design | Detected By |
|-------|--------|-----------|-------------|
| TypeScript Monorepo | Yes | Yes | `turbo.json`, `pnpm-workspace.yaml` |
| TypeScript Standalone (Next.js + NestJS) | Yes | Yes | `package.json` + `tsconfig.json` |
| .NET / C# (ASP.NET Core) | Yes | Yes | `*.csproj`, `*.sln` |
| Python (FastAPI / Django) | Yes | Yes | `pyproject.toml`, `requirements.txt` |
| Mobile (Expo / React Native) | Via platform skill | -- | `app.config.ts`, `eas.json` |
| Desktop (Tauri) | Via platform skill | -- | `tauri.conf.json`, `src-tauri/` |
| Chrome Extensions | Via platform skill | -- | `manifest.json` with extension fields |

## Engineering Standards

These skills reference standards from the [Engineering Standards](https://github.com/dannypenrose/engineering-standards) repository, which contains 51 documents covering the full development lifecycle. The standards are licensed separately under [CC BY-NC-ND 4.0](https://github.com/dannypenrose/engineering-standards/blob/main/LICENSE).

## Disclaimer

These skills have been developed and refined by Danny Penrose, who has over 20 years of experience in the technology industry. They are designed to improve the consistency and quality of AI-assisted development by providing structured context and enforcement rules.

However, large language models are probabilistic systems. Even with well-crafted skills and comprehensive standards, an AI agent may misapply rules, skip checks, apply guidance from the wrong stack, or produce output that looks correct but contains subtle deviations. Skills reduce the frequency of these issues -- they do not eliminate them.

**All AI-assisted output should be reviewed by an experienced software engineer before being committed, merged, or deployed.** These skills are a productivity tool, not a substitute for professional judgement. The author accepts no liability for defects, security vulnerabilities, or other issues arising from code produced with the assistance of these skills.

## License

[MIT](./LICENSE) -- use, modify, and share freely with attribution.
