# ADR-0002: Clean Architecture

## Status

Accepted

## Date

2026-02-18

## Context

Practice Desk needs to swap data sources (mock, API, database) without rewriting business logic or
UI code. A flat architecture with direct database access in route handlers or direct API calls in
components creates coupling that makes testing and migration painful.

## Decision

Adopt hexagonal (clean) architecture for both frontend and backend, with clearly defined layers and
a strict dependency rule: inner layers never depend on outer layers.

### Backend layers

```
domain (entities, value objects, business rules)
  ↓
application (use cases, port interfaces)
  ↓
infrastructure (SQLite repositories, external services)
  ↓
api (FastAPI routes, dependency injection)
```

### Frontend layers

```
domain (types from contracts, pure business functions)
  ↓
application (repository port interfaces)
  ↓
infrastructure (API client, mock repositories)
  ↓
adapters (Zustand stores, React hooks)
  ↓
components / screens (UI)
```

### Repository pattern

All data access goes through interfaces (TypeScript) or ABCs (Python). This enables swapping mock
data, API, or database implementations without touching business logic.

### Dependency rule

- Domain has zero external dependencies.
- Application depends only on domain.
- Infrastructure implements application ports.
- Adapters/API consume all inner layers and bridge to frameworks.

## Consequences

### Positive

- Each layer is independently testable with mock dependencies
- Data sources are swappable without touching business logic or UI
- Same architecture pattern in both layers — consistent mental model
- Business rules are isolated as pure functions, easy to unit test

### Negative

- More files and indirection than a flat structure
- New contributors must understand layering to know where code goes
- Over-engineering risk for early stages — justified by multi-source data requirement
