---
name: testing-and-quality
description: "Enforce testing strategy and quality standards when writing or modifying tests, or when implementing features that need test coverage. Use this skill whenever the user writes unit tests, integration tests, E2E tests, test fixtures, mocks, or discusses testing strategy, coverage requirements, TDD, code review processes, or performance testing. Also triggers when the user asks 'how should I test this', 'what tests do I need', or works on any test file — even if they don't explicitly mention 'testing standards'."
---

# Testing & Quality Standards

When this skill triggers, follow these steps to enforce quality standards across the codebase.

## Step 1: Read the Relevant Standards

Before giving any testing advice or writing any test code, **fetch the actual standards documents using WebFetch** — do not rely on memory or assumptions.

### Always read first:
- `https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/quality/testing-strategy.md`

### Read additionally based on context:
- **Code review discussions**: `https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/quality/code-review-guidelines.md`
- **Performance work**: `https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/quality/performance-budgets.md`
- **Load/stress testing**: `https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/reliability/load-testing.md`
- **CI/preflight pipelines**: `https://raw.githubusercontent.com/dannypenrose/engineering-standards/main/quality/ci-preflight-strategy.md`

After reading, apply the standards as written. If the standards conflict with anything below, **the standards documents win**.

## Step 2: Apply the Testing Pyramid

Enforce this distribution across the test suite:

```
        /  E2E  \        ~10%  — Critical user journeys only
       /----------\
      / Integration \    ~20%  — Module boundaries, API contracts
     /----------------\
    /    Unit Tests     \  ~70%  — Business logic, pure functions, components
   /____________________\
```

- **Unit tests** are fast, isolated, no I/O. Mock external dependencies.
- **Integration tests** verify module boundaries, database queries, API endpoints.
- **E2E tests** cover critical user paths only — they are slow and brittle, so keep them minimal.

If a PR adds only E2E tests for logic that should be unit-tested, flag it.

## Step 3: Enforce Coverage Targets

| Scope | Minimum Coverage |
|-------|-----------------|
| Overall project | 70% |
| Business logic / services | 80% |
| Critical paths (auth, billing, data mutations) | 100% |
| UI components | 60% (behavior, not snapshots) |
| Utilities / pure functions | 90% |

Coverage is a floor, not a ceiling. Never reduce coverage to merge faster.

## Step 4: Guide Test Structure

### Naming Convention

Use descriptive names that read as specifications:

```typescript
// GOOD — describes behavior
it('returns 401 when JWT token is expired')
it('calculates total with tax for multi-item cart')
it('disables submit button while form is submitting')

// BAD — describes implementation
it('calls validateToken')
it('tests calculateTotal')
it('checks button state')
```

### Arrange-Act-Assert Pattern

Every test should follow this structure:

```typescript
it('returns paginated results with correct metadata', () => {
  // Arrange — set up preconditions
  const items = createMockItems(25);
  const query = { page: 2, limit: 10 };

  // Act — execute the behavior under test
  const result = paginateItems(items, query);

  // Assert — verify the outcome
  expect(result.data).toHaveLength(10);
  expect(result.meta.currentPage).toBe(2);
  expect(result.meta.totalPages).toBe(3);
});
```

### What to Test

- **Test behavior, not implementation.** If refactoring internals breaks tests but not functionality, the tests are wrong.
- **Test edge cases**: empty inputs, boundary values, error states, concurrent access.
- **Test the contract**: inputs, outputs, side effects, error handling.
- **Do not test**: private methods directly, framework internals, trivial getters/setters.

## Step 5: Framework-Specific Guidance

### Frontend (Jest + React Testing Library)
- Use `screen.getByRole` over `getByTestId` — test accessibility, not implementation.
- Prefer `userEvent` over `fireEvent` for realistic interactions.
- Test component behavior: what renders, what happens on interaction, what gets called.

### Backend (Jest + @nestjs/testing)
- Use `@forge/testing` for mock factories (`createMockPrismaService`, `nestjsUserFixtures`).
- Test controllers through the NestJS testing module, not by calling methods directly.
- Test services with mocked repositories — verify queries and business logic.

### Shared Packages (Vitest)
- Packages like `@forge/ai` and `@forge/config` use Vitest.
- Keep package tests fast and isolated — no database, no network.

## Step 6: TDD Workflow (When Applicable)

When the user is doing TDD or you are guiding test-first development:

1. **Red** — Write a failing test that defines the desired behavior.
2. **Green** — Write the minimum code to make the test pass.
3. **Refactor** — Clean up while keeping tests green.

Do not skip the refactor step. Do not write production code before the test.

## Quick Reference Card

```
TESTING PYRAMID          COVERAGE FLOORS          PATTERN
-----------------        -----------------        -----------------
10% E2E                  70% overall              Arrange
20% Integration          80% business logic       Act
70% Unit                 100% critical paths      Assert

NAMING: it('does [behavior] when [condition]')
MOCK: External deps only — never mock the thing under test
FILE: *.spec.ts (unit/integration), *.e2e-spec.ts (E2E)
```
