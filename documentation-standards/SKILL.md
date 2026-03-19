---
name: documentation-standards
description: Enforce documentation standards when writing, updating, or reviewing documentation files (.md, .mdx), README files, changelogs, API docs, architecture decision records, code comments, or any technical writing. Use this skill whenever the user creates new documentation, restructures docs, writes READMEs, updates changelogs, adds code comments or JSDoc/XML annotations, configures Nextra navigation, writes Mermaid diagrams, or discusses documentation strategy — even if the user doesn't explicitly mention 'documentation standards'.
metadata:
  author: dannypenrose
  version: "1.0.0"
  argument-hint: <file-or-pattern>
---

# Documentation Standards Enforcement

Ensure all documentation follows the engineering documentation standards.

## How It Works

1. Fetch the documentation standards
2. Read the files being created or modified
3. Apply the standards as you write
4. Cite specific rules when correcting approach

## Step 1: Fetch the Standard

Use the WebFetch tool to load the documentation standard. Do NOT skip this step.

`https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/governance/documentation-standards.md`

## Step 2: Identify What You're Writing

| Documentation type | Key standards sections to focus on |
|---|---|
| Feature/module docs | Diataxis framework, feature doc template, directory structure |
| README files | README standards, required sections |
| Changelogs | Changelog standards (Keep a Changelog format) |
| Code comments / JSDoc | Code comments philosophy, JSDoc standards |
| API documentation | API documentation (OpenAPI/Swagger) |
| Architecture docs | Architecture documentation (C4 model, ADRs) |
| Nextra site pages | MDX/Nextra compatibility, navigation metadata, internal links |
| Mermaid diagrams | Mermaid diagram standards (v11.x compatibility) |
| Synced docs (monorepo) | Synced documentation pattern, internal link prefixing |

## Step 3: Apply Standards

Once you have read the standard:

- Follow every convention, template, and requirement from the loaded document.
- Apply the standards as you write. Do not dump a list of rules before starting.
- When the user's approach would violate a standard, mention the specific rule and explain why.

### Key Areas to Enforce

**Structure & Organisation:**
- Diataxis framework: tutorials (learning), how-to guides (tasks), explanations (understanding), references (information)
- Directory conventions: `concepts/`, `guides/`, `reference/`, `maintenance/`
- Every directory must have an `index.md` or `index.mdx`
- Single `#` heading per page, sequential heading levels

**Content Standards:**
- Write for readers, not writers
- Active voice, present tense
- Show, don't tell — use code examples
- Inclusive language (avoid gendered pronouns, ableist terms)
- UK English spelling for user-facing content

**MDX/Nextra Compatibility:**
- Escape bare `<` and `>` outside code blocks (use `&lt;`, `&gt;`, or backticks)
- Code blocks must have language identifiers
- Internal links use absolute paths with section prefix
- Synced docs must include subdirectory path in links

**Navigation Metadata (`_meta.ts`):**
- `index` entry always first
- Entries alphabetised by display title (within separator groups)
- `changelog` always last (if present)
- Titles derived from `# Heading` or kebab-case-to-Title-Case

**Changelogs:**
- Keep a Changelog format
- Monthly grouping, newest first
- Short descriptive heading + one-sentence summary
- Past tense, non-technical reader audience

**Code Comments:**
- Explain why, not what
- Comments describe intent, constraints, and non-obvious decisions
- JSDoc for public APIs (TypeScript)
- No redundant comments restating the code

**Mermaid Diagrams:**
- Use `---\nconfig:\n  ...\n---` frontmatter for configuration (not `%%{init:}%%`)
- Node IDs: alphanumeric + underscores only (no hyphens)
- Subgraph IDs: same rules (no hyphens)
- Quote labels containing special characters
- No `&` in labels (use `and`)

## Step 4: Cite When Correcting

If you need to push back on the user's approach:

- Reference the specific section from the standards document
- Explain the reasoning briefly
- Suggest the compliant alternative

Example: "The documentation standard requires Diataxis-compliant directory structure — this guide belongs in `guides/` not at the root of the docs folder (see: Directory Structure section)."

## Rules

1. **Always fetch the standard first.** Standards evolve — do not rely on memory.
2. **Never dump standards content.** Apply them silently, cite only when correcting.
3. **MDX safety is critical.** Unescaped `<` followed by digits/non-letters breaks Nextra builds.
4. **Internal links must include section prefix and subdirectory.** This is the most commonly missed rule.
5. **Be practical.** For quick comment additions, apply standards without commentary. Save explanations for structural decisions.
