# ADR-0003: TDD First

## Status

Accepted

## Date

2026-02-18

## Context

The previous POC had no automated tests. Changes broke existing functionality with no safety net,
and manual testing was unreliable and time-consuming. Without tests, refactoring was risky and
regressions were discovered late.

## Decision

All implementation follows Test-Driven Development. Write the test before writing the code. No
exceptions.

### TDD Cycle

1. **Red**: Write a failing test that describes the expected behavior.
2. **Green**: Write the minimum code to make it pass.
3. **Refactor**: Clean up while keeping tests green.

### Mandatory Test Levels

Every feature MUST have coverage at ALL applicable levels:

| Level           | Scope                                      | Location            |
| --------------- | ------------------------------------------ | ------------------- |
| **Unit**        | Pure functions, domain logic, isolated     | Next to source file |
| **Integration** | Component interactions, DB queries, API    | Next to source file |
| **Acceptance**  | User story validation, business rules      | Feature directory   |
| **E2E**         | Full user flows, browser-based             | `src/clients/app/`  |
| **Contract**    | Schema compliance, API contract validation | `src/contracts/`    |
| **Smoke**       | Critical path quick-check after deploy     | `scripts/` or CI    |

Additional test types (performance, security, accessibility) as needed.

### Rules

- Code written before its test must be deleted and restarted.
- A feature is not "done" until all applicable test levels pass.
- Test files live next to the source they test (not in a separate `tests/` tree).
- CI blocks merge if any test level fails.

## Consequences

### Positive

- Regressions caught immediately
- Refactoring is safe — tests act as a safety net
- Tests serve as living documentation of expected behavior
- Forces small, focused units of work
- Higher confidence in deployments

### Negative

- Initial development is slower (write test + implementation)
- Test maintenance overhead as the codebase evolves
- Developers must learn TDD discipline
