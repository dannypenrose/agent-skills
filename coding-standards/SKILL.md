---
name: coding-standards
description: >
  Enforce language-specific coding standards when writing, creating, implementing,
  building, or refactoring code. Use this skill whenever the user asks to write code,
  create a component, implement a feature, build a service, refactor existing code,
  or review code quality in any language — TypeScript/JavaScript, C#/.NET, Python,
  or any other stack. Also triggers when discussing naming conventions, file organization,
  project structure, code patterns, API design, or architecture decisions. This skill
  ensures all code follows the authoritative engineering standards. Activate for any
  task involving: new files, new functions, new classes, new modules, new endpoints,
  code changes, code review, pull request review, bug fixes that touch implementation,
  or restructuring existing code.
---

# Coding Standards Enforcement

You MUST follow this process whenever writing, reviewing, or modifying code.

## Step 1: Detect Tech Stack

Check the project root for these files to determine the tech stack:

| File(s) Present | Stack |
|---|---|
| `turbo.json` OR `pnpm-workspace.yaml` (with `package.json` + `tsconfig.json`) | TypeScript Monorepo |
| `package.json` + `tsconfig.json` (no monorepo markers) | TypeScript Standalone (Next.js + NestJS) |
| `*.csproj` OR `*.sln` | .NET / C# |
| `pyproject.toml` OR `requirements.txt` OR `setup.py` | Python |

If multiple stacks are present (e.g., a monorepo with a Python service), detect which stack the user is actively working in based on the files they reference.

## Step 2: Read the Relevant Standard

Based on the detected stack, use the WebFetch tool to load the appropriate coding standard. Do NOT skip this step. Do NOT summarize from memory. Actually fetch the file.

### Primary standard (fetch exactly ONE):

- **TypeScript Monorepo**:
  `https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/typescript/coding-standards-monorepo.md`

- **TypeScript Standalone (Next.js + NestJS)**:
  `https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/typescript/coding-standards-nextjs-nestjs.md`

- **.NET / C#**:
  `https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/dotnet/coding-standards.md`

- **Python**:
  `https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/python/coding-standards.md`

### Conditional standards (fetch IF relevant to the task):

- **Monorepo detected** (turbo.json or pnpm-workspace.yaml exists) — also fetch:
  `https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/development/code-sharing.md`

- **Task involves frontend state management** (React state, stores, context, reducers, signals) — also fetch:
  `https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/development/state-management.md`

- **Task involves adding/updating/managing dependencies** — also fetch:
  `https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/development/dependency-management.md`

- **Task involves git workflow, branching, or commit conventions** — also fetch:
  `https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/development/git-standards.md`

## Step 3: Apply Standards Silently

Once you have read the standard:

- Follow every naming convention, file structure pattern, and architectural requirement from the loaded document.
- Apply the patterns as you write code. Do not dump a list of rules before starting work.
- When the user's proposed approach would violate a standard, mention the specific rule and explain why the standard recommends a different approach.
- When making architectural decisions (e.g., where to place a file, how to name a function, how to structure a module), prefer the pattern documented in the standard over personal preference or general convention.

## Step 4: Cite When Correcting

If you need to push back on the user's approach because it conflicts with the standard:

- Reference the specific section or rule from the standards document.
- Explain the reasoning briefly.
- Suggest the compliant alternative.

Example: "The monorepo coding standard specifies that shared utilities go in `@forge/utils`, not in app-local `utils/` directories (see: Package Ownership section). Moving this to the shared package instead."

## Rules

1. **Never skip the Read step.** Even if you think you know the standard, read it. Standards evolve.
2. **Never duplicate standards content in your response.** Point to the file path if the user wants to read the full standard.
3. **One primary standard per task.** Do not read all four standards. Detect the stack and read only the matching one.
4. **Standards override general best practices.** If the standard says to do something a specific way, follow it even if common convention differs.
5. **Be practical, not pedantic.** For quick one-line fixes, apply the standard without commentary. Save explanations for when the user is making a structural or architectural choice that conflicts with the standard.
