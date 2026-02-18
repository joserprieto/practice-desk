# User Stories — Legal Practice Management

Scope: **Legal vertical only** (see
[ADR-0005](../../architecture/adrs/ADR-0005-legal-vertical-scope.md)).

Stories organized by epic. Each story: **As a [persona], I want to [action] so that [benefit].**

Priority: **Must** (UMFP), **Should** (post-UMFP), **Could** (future).

## Story Sets

| File                                                                     | Epic                                | Stories |  Must  | Should | Could  |
| ------------------------------------------------------------------------ | ----------------------------------- | :-----: | :----: | :----: | :----: |
| [us-ep01-expediente-management.md](us-ep01-expediente-management.md)     | Expediente Management               |   20    |   11   |   6    |   3    |
| [us-ep02-deadlines-calendar.md](us-ep02-deadlines-calendar.md)           | Procedural Deadlines & Calendar     |   16    |   8    |   4    |   4    |
| [us-ep03-proceedings-hearings.md](us-ep03-proceedings-hearings.md)       | Proceedings, Hearings & Resolutions |   18    |   8    |   6    |   4    |
| [us-ep04-document-management.md](us-ep04-document-management.md)         | Document Collection & Management    |   16    |   8    |   5    |   3    |
| [us-ep05-client-communication.md](us-ep05-client-communication.md)       | Client Communication                |   12    |   5    |   4    |   3    |
| [us-ep06-client-portal.md](us-ep06-client-portal.md)                     | Client Portal                       |   12    |   5    |   4    |   3    |
| [us-ep07-appointment-scheduling.md](us-ep07-appointment-scheduling.md)   | Appointment Scheduling              |   12    |   6    |   4    |   2    |
| [us-ep08-financial-management.md](us-ep08-financial-management.md)       | Financial Management                |   18    |   7    |   6    |   5    |
| [us-ep09-dashboard-reporting.md](us-ep09-dashboard-reporting.md)         | Dashboard & Reporting               |   12    |   3    |   4    |   5    |
| [us-ep10-administration-security.md](us-ep10-administration-security.md) | Administration & Security           |   14    |   5    |   5    |   4    |
| **Total**                                                                | **10 epics**                        | **150** | **66** | **48** | **36** |

## UMFP Scope

The 66 **Must** stories form the UMFP. They cover:

- Expediente lifecycle (create, classify, track, close)
- Procedural deadline management (calculation, alerts, audit trail)
- Proceedings and hearings (actuaciones, vistas, resoluciones)
- Document collection from clients
- Client communication and portal
- Appointment scheduling
- Time tracking and invoicing (VeriFactu compliant)
- Partner and associate dashboards
- Firm configuration and user management

## Naming Convention

- Files: `us-epNN-short-name.md`
- Story IDs: `US-N.YY` where N = epic number, YY = story number within epic
