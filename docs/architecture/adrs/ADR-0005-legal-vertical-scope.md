# ADR-0005: Legal Vertical Scope

## Status

Accepted

## Date

2026-02-18

## Context

The POC (`_SNAPSHOT-2026-02-17/`) attempted to serve three professional sectors simultaneously:
fiscal advisory (asesorías fiscales), labor advisory (asesorías laborales), and law firms (despachos
de abogados). This multi-sector approach resulted in:

- A generic data model that didn't capture any domain deeply enough
- Only 55 user stories that lacked domain-specific concepts (expedientes, procedimientos judiciales,
  plazos procesales, juzgados, procuradores, etc.)
- No coverage of critical legal workflows (actuaciones procesales, vistas, resoluciones judiciales,
  gestión económica con suplidos/provisiones de fondos, etc.)

Meanwhile, each sector has fundamentally different workflows:

- **Fiscal:** Recurring campaigns (quarterly tax collection from many clients), fiscal deadline
  calendar, batch document collection
- **Labor:** Worker registration, dual-source collection, sick leave/accident workflows with strict
  legal deadlines
- **Legal:** Expediente-centric, procedimiento tracking, plazos procesales with severe legal
  consequences, court hearing management, party management, appeal chains

Trying to serve all three simultaneously dilutes focus and produces a shallow product for all.

## Decision

Focus exclusively on the **legal vertical** (despachos de abogados / bufetes) for all analysis,
design, and implementation work from v0.2.0 onward.

### What this means

- All user stories target law firm workflows (expedientes, procedimientos, vistas, plazos)
- The data model captures legal domain entities (Juzgado, Procurador, ParteProcesal,
  ActuaciónProcesal, ResoluciónJudicial, etc.)
- Personas are law firm roles (associate, managing partner, paralegal, client, office admin)
- The Firm entity retains `sector` as an enum but only `legal` is actively developed
- Feature inventory and screens are designed for legal practice management

### What this does NOT mean

- The architecture does NOT become law-specific — the clean architecture layers, contract-first
  approach, and entity schemas remain sector-agnostic in design
- The `sector` enum in the Firm entity is NOT removed — `fiscal` and `labor` remain as values for
  future expansion
- No code is written that would prevent future sector expansion
- The pivot is about **analysis depth and implementation priority**, not architectural lock-in

### Future expansion path

When the legal vertical is validated and stable, fiscal and labor verticals can be added by:

1. Adding sector-specific entities (e.g., ObligaciónFiscal, AltaTrabajador)
2. Adding sector-specific user stories and features
3. Extending the Firm configuration for sector-specific defaults
4. Reusing the core platform (auth, notifications, documents, messaging)

## Consequences

### Positive

- Deep domain modeling for legal practice — captures the real complexity of a law firm
- 150 user stories (up from 55) covering expedientes, plazos, vistas, facturación, etc.
- Data model with 21 entities (up from 9) properly representing legal workflows
- Clear scope for UMFP — no ambiguity about what to build first
- Better competitive positioning against LexON, Aranzadi Fusion, MN program

### Negative

- Fiscal and labor advisors cannot use the platform until their verticals are developed
- Some POC work on campaigns and batch collection is not immediately reusable
- Narrower initial market (law firms only vs. all professional services)

## Related ADRs

- ADR-0001: Contracts as SSOT — contracts remain sector-agnostic in schema design
- ADR-0002: Clean Architecture — layers remain generic, only domain entities are legal-specific
