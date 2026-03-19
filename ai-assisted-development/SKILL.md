---
name: ai-assisted-development
description: Enforce AI-assisted development standards when working with AI coding agents, reviewing AI-generated code, writing prompts for AI tools, or configuring AI development environments. Use this skill whenever the user discusses AI code review, prompt engineering, agentic coding patterns, CLAUDE.md configuration, sub-agent orchestration, MCP server setup, or AI workflow quality gates. Also triggers when reviewing code that was AI-generated, setting up Claude Code projects, configuring coding assistants, or discussing AI failure modes and mitigations — even if the user doesn't explicitly mention 'AI standards'.
metadata:
  author: dannypenrose
  version: "1.0.0"
  argument-hint: <file-or-pattern>
---

# AI-Assisted Development Standards Enforcement

Ensure all AI-assisted development practices comply with the engineering standards.

## How It Works

1. Determine which AI standard(s) are relevant to the task
2. Fetch the applicable standard(s)
3. Apply the standards as you work
4. Cite specific rules when correcting approach

## Step 1: Identify Applicable Standards

| Task involves... | Fetch standard |
|-------------------|---------------|
| Setting up AI coding agents, CLAUDE.md, sub-agent orchestration, MCP servers | Agentic Coding Standards |
| Reviewing AI-generated code, validating AI output, checking for AI failure modes | AI Code Review |
| Configuring Claude Code, project setup, feature indexes, quality gates | Claude Code Standards |
| Writing prompts, structuring instructions, context management | Prompt Engineering |

Multiple standards may apply to a single task. Fetch all that are relevant.

## Step 2: Fetch the Relevant Standards

Use the WebFetch tool to load the applicable standard(s). Do NOT skip this step.

- **Agentic Coding Standards**:
  `https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/ai/agentic-coding-standards.md`

- **AI Code Review**:
  `https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/ai/ai-code-review.md`

- **Claude Code Standards**:
  `https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/ai/claude-code-standards.md`

- **Prompt Engineering**:
  `https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/ai/prompt-engineering.md`

## Step 3: Apply Standards

Once you have read the standard(s):

- Follow every pattern, convention, and requirement from the loaded document(s).
- Apply the standards as you work. Do not dump a list of rules before starting.
- When the user's approach would violate a standard, mention the specific rule and explain why the standard recommends a different approach.

### Key Areas to Enforce

**Agentic Coding Standards:**
- Standards-driven development (skills detect → fetch → apply → cite)
- Context architecture (required layers: global, project, app-specific)
- Workflow standards (pre-implementation, during, post-implementation)
- AI failure mode awareness and mitigations
- Security considerations for AI-generated code

**AI Code Review:**
- AI-specific failure modes: pattern invention, over-engineering, phantom APIs, stale knowledge, shallow error handling, test theatre, context drift, confident incorrectness
- Review checklist: correctness, pattern conformance, security, testing, performance, maintainability
- Review depth selection (quick, standard, deep)

**Claude Code Standards:**
- CLAUDE.md structure and required sections
- Feature index standards
- Sub-agent orchestration (when to use, specialist selection, coordination strategies)
- Quality gate workflows (pre/during/post implementation)
- MCP server integration and fallback strategies
- Git integration (hooks, commit standards)
- Performance optimisation (token efficiency, context management)

**Prompt Engineering:**
- Prompt structure: Context + Constraint + Instruction + Output Format
- Task decomposition for complex work
- Constraint types (positive, negative, reference, scope)
- Context window management
- Anti-patterns to avoid

## Step 4: Cite When Correcting

If you need to push back on the user's approach:

- Reference the specific section from the standards document
- Explain the reasoning briefly
- Suggest the compliant alternative

Example: "The AI Code Review standard identifies 'pattern invention' as a common AI failure mode — the suggested API doesn't exist in this framework version. Let me verify against the actual documentation first."

## Rules

1. **Always fetch the standard first.** Standards evolve — do not rely on memory.
2. **Never dump standards content.** Apply them silently, cite only when correcting.
3. **Multiple standards can apply.** Fetch all relevant ones for the task.
4. **AI failure modes are critical.** Always watch for pattern invention, phantom APIs, and confident incorrectness when reviewing AI output.
5. **Be practical.** These standards exist to catch real problems, not to slow down work.
