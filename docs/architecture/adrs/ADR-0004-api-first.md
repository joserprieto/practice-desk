# ADR-0004: API First Design

## Status

Accepted

## Date

2026-02-18

## Context

In the previous POC, API endpoints were created ad-hoc as the frontend needed them. This led to
inconsistent response shapes, missing error handling, and undocumented contracts between frontend
and backend.

## Decision

Design the API specification before implementing any endpoint. The spec is the contract between
frontend and backend teams.

### Workflow

1. **Define**: Write the API spec (OpenAPI for REST, AsyncAPI for WebSocket) in
   `docs/architecture/api/`.
2. **Review**: Spec is reviewed and approved before implementation begins.
3. **Implement backend**: Build endpoints against the spec. Contract tests verify compliance.
4. **Implement frontend**: Build API client against the spec. Contract tests verify compliance.

### Specs

| Spec     | Format       | Location                 | Purpose          |
| -------- | ------------ | ------------------------ | ---------------- |
| REST API | OpenAPI 3.1  | `docs/architecture/api/` | HTTP endpoints   |
| Events   | AsyncAPI 3.0 | `docs/architecture/api/` | WebSocket events |

### Rules

- No endpoint may be implemented without a spec entry.
- Request/response types reference contracts in `src/contracts/` via `$ref`.
- Breaking changes require a spec update, review, and contract test update.
- CI verifies that implementation matches the spec (contract tests).

## Consequences

### Positive

- Frontend and backend can develop in parallel against a shared contract
- API documentation is always up-to-date (it IS the spec)
- Contract tests catch drift between spec and implementation
- Breaking changes are visible and reviewable in PRs

### Negative

- Additional ceremony before implementation (write spec first)
- Spec maintenance overhead when APIs evolve
- Tooling needed for spec validation and contract testing
