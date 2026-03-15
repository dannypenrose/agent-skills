---
name: api-standards
description: >
  Enforce API design standards when creating, modifying, or reviewing REST APIs.
  Use this skill whenever the user works on endpoints, controllers, routes, DTOs,
  request/response types, API versioning, error responses, pagination, middleware,
  guards, interceptors, or any HTTP API surface. Covers NestJS, ASP.NET Core, and
  FastAPI patterns. Also applies when discussing API contracts, OpenAPI specs,
  versioning strategy, deprecation, or REST conventions — even if the user doesn't
  explicitly mention 'API standards'.
---

# API Standards Skill

When this skill activates, you MUST detect the project's tech stack and read the
appropriate API design standard document before writing or reviewing any API code.
Do not summarize the standards from memory — always read the actual file.

## Step 1: Detect Tech Stack

Examine the project to determine which stack is in use:

### TypeScript Monorepo Detection
Look for ANY of these signals:
- `turbo.json` or `pnpm-workspace.yaml` at the repo root
- A `packages/` directory with shared `@forge/*` or similar scoped packages
- Multiple `apps/` directories each with their own `package.json`
- NestJS backend (`@nestjs/core` in dependencies) combined with Next.js frontend

### TypeScript Standalone Detection
Look for these signals WITHOUT monorepo indicators:
- A single `package.json` at the root (no workspace config)
- `@nestjs/core` or `next` in dependencies
- No `turbo.json`, no `pnpm-workspace.yaml`, no `packages/` directory

### .NET Detection
- `*.csproj` or `*.sln` files present
- `Program.cs`, `Startup.cs`, or `appsettings.json`
- `ASP.NET Core` or `Microsoft.AspNetCore` references

### Python Detection
- `pyproject.toml`, `setup.py`, or `requirements.txt`
- `FastAPI`, `Django`, or `Flask` in dependencies
- `main.py` or `app.py` with router/endpoint definitions

## Step 2: Read the Correct Standard

Based on the detected stack, FETCH the corresponding standard document using the
WebFetch tool. Do not skip this step. Do not paraphrase from memory.

| Stack | Standard Document (URL) |
|-------|------------------------|
| TypeScript Monorepo | `https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/typescript/api-design-monorepo.md` |
| TypeScript Standalone | `https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/typescript/api-design-nextjs-nestjs.md` |
| .NET | `https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/dotnet/api-design.md` |
| Python | `https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/python/api-design.md` |

If the stack cannot be determined, ask the user which standard applies.

## Step 3: Read Conditional Standards

If the task involves API versioning (version prefixes, deprecation, migration,
breaking changes, or the user mentions versioning), also READ:

- `https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/development/api-versioning.md`

If the task involves caching (Cache-Control headers, ETag, conditional requests,
CDN caching, API response caching, or stale-while-revalidate), also READ:

- `https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/development/caching-strategy.md`

## Step 4: Apply Standards

With the standard document loaded, apply it naturally during API development.
Do NOT lecture the user about standards or list rules unprompted. Instead:

### During Endpoint Creation
- Use the correct HTTP methods and status codes per the standard
- Structure error responses in the standard format
- Apply the standard's pagination pattern for list endpoints
- Name routes and parameters following the standard's conventions
- Validate request bodies using the standard's DTO/validation approach

### During Endpoint Modification
- Ensure changes maintain consistency with the standard
- If a pattern deviates from the standard, fix it or flag it
- Preserve backward compatibility unless explicitly breaking

### During Code Review
- Check endpoints against the standard's requirements
- Flag deviations with specific references to what the standard prescribes
- Suggest concrete fixes, not abstract advice

### Key Areas to Enforce
- **Error responses**: Consistent shape across all endpoints
- **Pagination**: Standard query parameters and response envelope
- **Naming**: Resource-based URLs, plural nouns, kebab-case paths
- **Status codes**: Correct codes for each operation type
- **Validation**: Request body and query parameter validation via DTOs/schemas
- **Guards/Middleware**: Authentication and authorization patterns per stack
- **Response types**: Consistent response envelope or direct returns per standard
- **Versioning**: If present, follows the versioning standard conventions

### What NOT to Do
- Do not dump the entire standard document into the conversation
- Do not preface every action with "According to the API standards..."
- Do not refuse to write code that slightly deviates if the user has good reason
- Do not apply standards from the wrong stack (e.g., .NET patterns in a NestJS project)
