# ADR-0001: Contracts as Single Source of Truth

## Status

Accepted

## Date

2026-02-18

## Context

Practice Desk has both a Python backend (FastAPI) and a TypeScript frontend (React). Without a
shared schema, type definitions will drift between layers, causing serialization bugs and
inconsistent behavior.

The previous POC maintained types inline in TypeScript only. Adding a backend required duplicating
all type definitions in Python manually.

## Decision

YAML schemas in `src/contracts/` are the single source of truth (SSOT) for all domain entities.

### Structure

```
src/contracts/
├── schemas/           # YAML entity schemas (JSON Schema-compatible)
├── generated/         # TypeScript types + Python Pydantic models
└── scripts/           # Generation scripts
```

### Rules

1. **Contract First**: Define the YAML schema before writing any implementation code.
2. **Generation pipeline**: YAML schemas generate both TypeScript types and Python Pydantic models.
3. **Generated files are committed**: No build-time dependency on generation scripts. PRs show type
   diffs.
4. **CI verifies sync**: Regeneration must produce identical output (drift detection).

### Conventions

| Aspect       | Convention                       | Example                  |
| ------------ | -------------------------------- | ------------------------ |
| Schema files | kebab-case                       | `document-request.yaml`  |
| Type names   | PascalCase                       | `DocumentRequest`        |
| Properties   | camelCase (TS) / snake_case (Py) | `dueDate` / `due_date`   |
| Enum values  | snake_case                       | `in_progress`            |
| Entity IDs   | UUIDv7                           | `019c63c8-5b3f-7123-...` |

## Consequences

### Positive

- One schema change propagates to both layers — contract drift eliminated
- CI can verify generated files are in sync with schemas
- New languages/platforms can be added with additional generators
- Schema is readable, reviewable, and versionable

### Negative

- YAML schemas must stay in sync with generated code — developers run generation after changes
- Additional tooling to maintain (generation scripts)
- Schema expressiveness limited by what both TypeScript and Python can represent
