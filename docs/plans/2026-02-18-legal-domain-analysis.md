# Legal Domain Analysis Rewrite — Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan
> task-by-task.

**Goal:** Rewrite all v0.2.0 analysis artifacts (user stories, features, screens, domain dictionary,
data model) with comprehensive Spanish legal practice (bufete de abogados) domain depth — replacing
the current generic 55-story set with 150 domain-specific user stories across 10 epics.

**Architecture:** Documentation-only changes. All files live under `docs/`. User stories split into
per-epic files following the POC pattern. Data model expanded from 9 to 21 entities to properly
model the legal domain (expedientes, procedimientos, juzgados, plazos procesales, etc.).

**Scope decision:** LEGAL vertical only. Fiscal and labor verticals are explicitly out of scope and
documented via ADR-0005.

**Reference material:**

- POC snapshot: `_SNAPSHOT-2026-02-17/` (local, not versioned)
- Web research on Spanish legal practice management (LexON, Aranzadi Fusion, MN program, Clio, etc.)
- Spanish legal system specifics (LexNET, CTEAJE, VeriFactu, plazos procesales, jurisdicciones)

---

## Task 1: Create ADR-0005 — Legal-Only Vertical Scope

**Files:**

- Create: `docs/architecture/adrs/ADR-0005-legal-vertical-scope.md`
- Modify: `docs/architecture/adrs/README.md` (add to index)

**Step 1: Write ADR-0005**

Create with this content:

```markdown
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
```

**Step 2: Update ADR README index**

Add this row to the index table in `docs/architecture/adrs/README.md`:

```markdown
| [0005](ADR-0005-legal-vertical-scope.md) | Legal Vertical Scope | Accepted | 2026-02-18 |
```

**Step 3: Verify formatting**

Run: `npx markdownlint-cli2 "docs/architecture/adrs/**/*.md"` — Expected: 0 errors

**Step 4: Commit**

```bash
git add docs/architecture/adrs/ADR-0005-legal-vertical-scope.md docs/architecture/adrs/README.md
git commit -m "docs(adr): add ADR-0005 legal vertical scope decision"
```

---

## Task 2: Update Personas

**Files:**

- Modify: `docs/analysis/personas/personas.md`

**Step 1: Rewrite personas with legal context**

The personas remain the same 5 roles but with enriched legal context. Rewrite the file with:

- **P1: Managing Partner (Socio Director)** — Priority: High. Licensed attorney (número de
  colegiado). Oversees firm operations, reviews invoices, monitors deadlines across the firm,
  evaluates associate performance (billable hours, case outcomes). Cares about: workload
  distribution, deadline compliance, financial KPIs (revenue, utilization, realization, collection
  rates), client satisfaction, RGPD compliance.

- **P2: Associate (Abogado Asociado)** — Priority: Critical. Primary daily user. Licensed attorney
  with active caseload. Manages expedientes end-to-end: creates cases, registers parties and courts,
  tracks procedural phases, records actuaciones, manages plazos procesales (the most critical
  workflow — missed deadlines cause preclusión, caducidad, professional liability). Schedules
  hearings, communicates with clients and procurador, drafts legal documents, tracks billable time.
  Cares about: not missing deadlines, case organization, efficient document drafting, clear case
  status visibility.

- **P3: Paralegal (Auxiliar Jurídico / Secretario/a)** — Priority: High. Supports associates with
  document management, deadline tracking, appointment scheduling, invoice generation, client
  communication. Monitors incoming court notifications. Maintains court directory (juzgados) and
  procurador contacts. Coordinates hearing logistics. Cares about: efficient task management,
  helping associates stay on top of deadlines, document organization.

- **P4: Client (Cliente)** — Priority: Critical. External user. Individual or company receiving
  legal services. Interacts through client portal: views case status, uploads requested documents,
  receives hearing notifications, communicates with lawyer, views invoices. May be non-technical,
  uses phone primarily. Cares about: knowing case status without calling, easy document submission,
  understanding costs, clear hearing information.

- **P5: Office Admin (Administrador/a de Oficina)** — Priority: Medium. Firm configuration and
  operations. Manages user accounts, client registry, court directory data, notification settings.
  Generates management reports. Imports data from legacy systems. Cares about: system configuration,
  data accuracy, reporting, smooth firm operations.

Keep the existing format (role description table, scenario, pain points, goals) but update content
for legal context.

**Step 2: Verify formatting**

Run: `npx markdownlint-cli2 "docs/analysis/personas/personas.md"` — Expected: 0 errors

**Step 3: Commit**

```bash
git add docs/analysis/personas/personas.md
git commit -m "docs(personas): update personas with legal domain context"
```

---

## Task 3: Create User Stories README (Index)

**Files:**

- Delete: `docs/analysis/user-stories/user-stories.md` (the old 55-story single file)
- Create: `docs/analysis/user-stories/README.md`

**Step 1: Delete old file and create new index**

Delete `docs/analysis/user-stories/user-stories.md`.

Create `docs/analysis/user-stories/README.md` with:

```markdown
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
```

**Step 2: Verify formatting**

Run: `npx markdownlint-cli2 "docs/analysis/user-stories/README.md"` — Expected: 0 errors

**Step 3: Commit**

```bash
git rm docs/analysis/user-stories/user-stories.md
git add docs/analysis/user-stories/README.md
git commit -m "docs(user-stories): replace single file with per-epic structure and index"
```

---

## Task 4: User Stories — EP-01 Expediente Management

**Files:**

- Create: `docs/analysis/user-stories/us-ep01-expediente-management.md`

**Step 1: Write EP-01 stories**

Create the file with all 20 stories. Use this structure:

```markdown
# EP-01: Expediente Management (Gestión de Expedientes)

The core unit of work in a law firm. An "expediente" is the complete dossier for a client engagement
— broader than a single court proceeding, it may contain multiple procedimientos (proceedings),
actuaciones (procedural actions), and documents.

## Must (UMFP)

- **US-1.01** — As an **associate**, I want to create a new expediente with reference number, area
  of law, matter type, client, and description so that I can start tracking a legal engagement.

- **US-1.02** — As an **associate**, I want to assign a procurador to an expediente so that court
  representation is coordinated from the start.

- **US-1.03** — As an **associate**, I want to register all parties (demandante, demandado,
  codemandados, terceros intervinientes) with their procedural roles so that the case file is
  complete.

- **US-1.04** — As an **associate**, I want to record the assigned court (juzgado), case number
  (número de autos), and judge so that I can track where the proceeding is being handled.

- **US-1.05** — As an **associate**, I want to see all my active expedientes with status, next
  deadline, and court so that I can prioritize my work.

- **US-1.06** — As an **associate**, I want to update an expediente's status (abierto, en trámite,
  pendiente resolución, cerrado, archivado) so that everyone knows where it stands.

- **US-1.07** — As a **managing partner**, I want to see all active expedientes across the firm,
  grouped by associate, so that I can monitor workload distribution.

- **US-1.08** — As a **managing partner**, I want to reassign an expediente from one associate to
  another so that workload stays balanced.

- **US-1.09** — As an **associate**, I want to run a conflict of interest check against all contacts
  and parties in the system when opening a new expediente so that I comply with deontological
  obligations.

- **US-1.10** — As a **paralegal**, I want to see expedientes assigned to my associate(s) so that I
  can support them proactively.

- **US-1.11** — As an **associate**, I want to classify expedientes by jurisdiction (civil, penal,
  laboral, contencioso-administrativo, mercantil) and matter subtype so that I can filter and report
  by category.

## Should

- **US-1.12** — As an **associate**, I want to link related expedientes (e.g., first instance →
  appeal → cassation) so that I can follow the full procedural chain.

- **US-1.13** — As an **associate**, I want to record the hoja de encargo profesional (engagement
  letter) with fee structure and terms so that the client relationship is properly formalized.

- **US-1.14** — As a **managing partner**, I want to see a timeline/history of all actions on an
  expediente so that I can audit what happened and when.

- **US-1.15** — As an **associate**, I want to add internal notes to an expediente that are not
  visible to the client so that I can keep a private work log.

- **US-1.16** — As an **associate**, I want to record the opposing counsel (abogado contrario) and
  their procurador so that I have their contact information readily available.

- **US-1.17** — As an **associate**, I want to search across all expedientes and documents for prior
  work on similar matters so that I can reuse strategies and precedents.

## Could

- **US-1.18** — As a **managing partner**, I want to see aggregate statistics (avg. time to
  resolution, expedientes per category, per associate) so that I can make data-driven decisions.

- **US-1.19** — As an **associate**, I want to record the cuantía (amount in dispute) so that I can
  track the financial exposure of each case.

- **US-1.20** — As a **paralegal**, I want to perform initial data entry for new expedientes and
  client onboarding (hoja de encargo, KYC documentation) so that the intake process is efficient.

## Summary

| Priority  | Count  |
| --------- | :----: |
| Must      |   11   |
| Should    |   6    |
| Could     |   3    |
| **Total** | **20** |
```

**Step 2: Verify formatting**

Run: `npx markdownlint-cli2 "docs/analysis/user-stories/us-ep01-expediente-management.md"`

**Step 3: Commit**

```bash
git add docs/analysis/user-stories/us-ep01-expediente-management.md
git commit -m "docs(user-stories): add EP-01 expediente management (20 stories)"
```

---

## Task 5: User Stories — EP-02 Procedural Deadlines & Calendar

**Files:**

- Create: `docs/analysis/user-stories/us-ep02-deadlines-calendar.md`

**Step 1: Write EP-02 stories**

```markdown
# EP-02: Procedural Deadlines & Calendar (Plazos Procesales y Agenda)

The most critical workflow for a Spanish law firm. Missed procedural deadlines cause preclusión
(loss of the right to act), caducidad de la instancia (case abandonment), and professional liability
claims against the lawyer. Deadline calculation must account for días hábiles (working days),
weekends, holidays, and the August judicial recess (inhábil).

## Must (UMFP)

- **US-2.01** — As an **associate**, I want to register a plazo procesal with start date, duration,
  type (días hábiles or naturales), and legal consequence of missing it so that I can track all my
  obligations.

- **US-2.02** — As an **associate**, I want the system to calculate deadline dates automatically,
  excluding weekends, holidays, and the August judicial recess so that deadlines are accurate.

- **US-2.03** — As an **associate**, I want to receive escalating alerts for approaching deadlines
  (7 days, 3 days, 1 day, same day) so that I never miss a plazo procesal.

- **US-2.04** — As an **associate**, I want to see all my pending deadlines in a calendar view (day,
  week, month) so that I can plan my work.

- **US-2.05** — As an **associate**, I want to mark a deadline as completed with evidence (e.g.,
  LexNET submission receipt) so that there is a verifiable audit trail.

- **US-2.06** — As a **managing partner**, I want to see a firm-wide deadline dashboard showing all
  pending, approaching, and overdue plazos so that nothing slips through.

- **US-2.07** — As a **paralegal**, I want to track and remind lawyers of pending deadlines
  approaching their due dates so that I can support the team proactively.

- **US-2.08** — As an **associate**, I want deadlines to be automatically created when a court
  notification is received (e.g., 20 days to file contestación after emplazamiento) so that I don't
  have to calculate them manually.

## Should

- **US-2.09** — As an **associate**, I want to see the legal consequence of each deadline
  (preclusión, caducidad, prescripción) so that I can prioritize by severity.

- **US-2.10** — As a **managing partner**, I want a deadline compliance report showing met vs.
  missed deadlines per associate so that I can monitor quality.

- **US-2.11** — As an **associate**, I want to differentiate between procedural deadlines (plazos
  procesales), prescription periods (prescripción), and internal deadlines so that each gets
  appropriate urgency.

- **US-2.12** — As an **associate**, I want to delegate a deadline task to a paralegal while
  retaining responsibility so that routine tasks are handled efficiently.

## Could

- **US-2.13** — As an **associate**, I want a "calculadora de términos" that computes the exact due
  date given a start date, number of days, and type (hábiles/naturales) so that I can verify
  calculations.

- **US-2.14** — As an **associate**, I want to configure my personal alert preferences (email,
  in-app, both) for deadline notifications so that I receive them through my preferred channel.

- **US-2.15** — As a **paralegal**, I want to see a combined deadline calendar for all associates I
  support so that I can coordinate across multiple lawyers.

- **US-2.16** — As a **managing partner**, I want to receive immediate alerts for any deadline that
  becomes overdue across the firm so that I can intervene when needed.

## Summary

| Priority  | Count  |
| --------- | :----: |
| Must      |   8    |
| Should    |   4    |
| Could     |   4    |
| **Total** | **16** |
```

**Step 2: Verify & Commit**

```bash
npx markdownlint-cli2 "docs/analysis/user-stories/us-ep02-deadlines-calendar.md"
git add docs/analysis/user-stories/us-ep02-deadlines-calendar.md
git commit -m "docs(user-stories): add EP-02 procedural deadlines & calendar (16 stories)"
```

---

## Task 6: User Stories — EP-03 Proceedings, Hearings & Resolutions

**Files:**

- Create: `docs/analysis/user-stories/us-ep03-proceedings-hearings.md`

**Step 1: Write EP-03 stories**

```markdown
# EP-03: Proceedings, Hearings & Resolutions (Actuaciones, Vistas y Resoluciones)

Tracking everything that happens inside a legal proceeding: actuaciones procesales (procedural
actions like filing a demanda, receiving a sentencia), vistas (court hearings), and resoluciones
judiciales (court decisions). Each expediente may contain multiple proceedings (e.g., main case +
interim measures + appeal).

## Must (UMFP)

- **US-3.01** — As an **associate**, I want to record actuaciones procesales (demanda, contestación,
  prueba, sentencia, recurso) in chronological order on each expediente so that I have a complete
  case history.

- **US-3.02** — As an **associate**, I want to track the current procedural phase of each proceeding
  (instrucción, audiencia previa, juicio oral, sentencia, ejecución) so that I know where each case
  stands.

- **US-3.03** — As an **associate**, I want to schedule a vista (hearing) with date, time, court,
  courtroom, and type (audiencia previa, juicio oral, comparecencia) so that all parties know when
  and where.

- **US-3.04** — As an **associate**, I want to receive notifications about upcoming hearings (7
  days, 1 day, 2 hours before) so that I can prepare and attend.

- **US-3.05** — As an **associate**, I want to record the outcome of a resolución judicial
  (sentencia, auto, providencia) including whether it is firme (final) or recurrible (appealable) so
  that I can determine next steps.

- **US-3.06** — As an **associate**, I want to record when a resolution is notified and have the
  system auto-create the corresponding appeal deadline so that I don't miss the window to appeal.

- **US-3.07** — As a **paralegal**, I want to coordinate hearing logistics with the client,
  procurador, and witnesses so that everyone is prepared and present.

- **US-3.08** — As an **associate**, I want to track multiple proceedings within one expediente
  (e.g., main proceeding + interim measures + appeal) so that the full procedural picture is
  visible.

## Should

- **US-3.09** — As an **associate**, I want to prepare for hearings by accessing a checklist of
  evidence to present, witnesses to summon, and expert reports so that I don't miss anything.

- **US-3.10** — As an **associate**, I want to record the result of each hearing (celebrada,
  suspendida, aplazada) with notes so that there is a record of what happened.

- **US-3.11** — As an **associate**, I want to track the tipo de procedimiento (ordinario, verbal,
  monitorio, abreviado, etc.) so that the correct procedural rules are applied.

- **US-3.12** — As an **associate**, I want to file a recurso (appeal) and have the system create
  the corresponding new proceeding linked to the original so that the procedural chain is
  maintained.

- **US-3.13** — As a **paralegal**, I want to maintain a directory of juzgados with name,
  jurisdiction, address, partido judicial, and contact information so that court data is accurate
  and centralized.

- **US-3.14** — As an **associate**, I want to communicate with the procurador through the platform,
  sharing documents and confirming procedural steps so that coordination is traceable.

## Could

- **US-3.15** — As an **associate**, I want to see a timeline visualization of all actuaciones and
  resoluciones for a proceeding so that I can quickly understand the case progression.

- **US-3.16** — As an **associate**, I want to track execution of sentence (ejecución de sentencia)
  including embargo proceedings so that post-judgment enforcement is managed.

- **US-3.17** — As a **managing partner**, I want to see statistics on case outcomes (estimado,
  desestimado, parcialmente estimado) by area of law and by associate so that I can identify
  strengths and improvement areas.

- **US-3.18** — As an **associate**, I want to record the parties' attendance at each hearing so
  that there is a record of who appeared.

## Summary

| Priority  | Count  |
| --------- | :----: |
| Must      |   8    |
| Should    |   6    |
| Could     |   4    |
| **Total** | **18** |
```

**Step 2: Verify & Commit**

```bash
npx markdownlint-cli2 "docs/analysis/user-stories/us-ep03-proceedings-hearings.md"
git add docs/analysis/user-stories/us-ep03-proceedings-hearings.md
git commit -m "docs(user-stories): add EP-03 proceedings, hearings & resolutions (18 stories)"
```

---

## Task 7: User Stories — EP-04 Document Collection & Management

**Files:**

- Create: `docs/analysis/user-stories/us-ep04-document-management.md`

**Step 1: Write EP-04 stories**

```markdown
# EP-04: Document Collection & Management (Recopilación y Gestión Documental)

Requesting documents from clients, receiving uploads, reviewing for acceptance, generating legal
documents from templates, and organizing everything by expediente. Covers both client-submitted
documents and firm-generated legal writings.

## Must (UMFP)

- **US-4.01** — As an **associate**, I want to create a document request for a client with a
  checklist of required documents and a due date so that the client knows exactly what to submit and
  by when.

- **US-4.02** — As a **client**, I want to see a clear checklist of documents I need to submit, with
  descriptions of each one, so that I don't submit the wrong thing.

- **US-4.03** — As a **client**, I want to upload documents from my phone or computer so that I can
  submit them without emailing attachments.

- **US-4.04** — As an **associate**, I want to see which documents have been submitted and which are
  still pending for each expediente so that I know what's blocking progress.

- **US-4.05** — As a **paralegal**, I want to review a submitted document and mark it as accepted or
  rejected (with a reason) so that the client knows if they need to resubmit.

- **US-4.06** — As a **client**, I want to receive a notification when a document is rejected with
  the reason so that I can fix and resubmit quickly.

- **US-4.07** — As an **associate**, I want to receive a notification when all requested documents
  for an expediente have been submitted so that I can proceed with the work.

- **US-4.08** — As an **associate**, I want to organize documents by expediente with a clear folder
  structure so that I can find any document quickly.

## Should

- **US-4.09** — As a **paralegal**, I want to send automatic reminders to clients who have pending
  documents past the due date so that I don't have to follow up manually.

- **US-4.10** — As an **associate**, I want to generate legal documents (demanda, contestación,
  recurso, contrato) from templates pre-filled with expediente data so that I can draft efficiently.

- **US-4.11** — As an **associate**, I want to download all documents for an expediente as a ZIP
  file so that I can work with them offline or share them externally.

- **US-4.12** — As a **paralegal**, I want to define document checklist templates (e.g., "Standard
  client onboarding: DNI, poder, hoja de encargo") so that I can reuse them across expedientes.

- **US-4.13** — As an **associate**, I want to track document versions so that I can see the history
  of changes to a document.

## Could

- **US-4.14** — As an **associate**, I want to preview uploaded documents (PDF, images) in the app
  without downloading them so that quick reviews are faster.

- **US-4.15** — As a **managing partner**, I want to see firm-wide document collection metrics (avg.
  time to completion, most delayed document types) so that I can identify bottlenecks.

- **US-4.16** — As an **associate**, I want to record how each document was filed (LexNET,
  presencial, correo) and the filing date so that there is a submission audit trail.

## Summary

| Priority  | Count  |
| --------- | :----: |
| Must      |   8    |
| Should    |   5    |
| Could     |   3    |
| **Total** | **16** |
```

**Step 2: Verify & Commit**

```bash
npx markdownlint-cli2 "docs/analysis/user-stories/us-ep04-document-management.md"
git add docs/analysis/user-stories/us-ep04-document-management.md
git commit -m "docs(user-stories): add EP-04 document collection & management (16 stories)"
```

---

## Task 8: User Stories — EP-05 Client Communication

**Files:**

- Create: `docs/analysis/user-stories/us-ep05-client-communication.md`

**Step 1: Write EP-05 stories**

```markdown
# EP-05: Client Communication (Comunicación con Clientes)

Traceable messaging between firm staff and clients within the context of expedientes. Replaces
scattered email and phone calls with organized, auditable communication.

## Must (UMFP)

- **US-5.01** — As an **associate**, I want to send a message to a client within the context of an
  expediente so that communication is traceable and organized.

- **US-5.02** — As a **client**, I want to receive notifications about new messages from my lawyer
  so that I don't miss important updates.

- **US-5.03** — As a **client**, I want to reply to a message from my lawyer through the app so that
  I don't have to use email or phone.

- **US-5.04** — As an **associate**, I want to see the full message history for an expediente so
  that I have context before communicating with the client.

- **US-5.05** — As an **associate**, I want to notify a client when there is a significant case
  update (new resolution, upcoming hearing, milestone) so that clients stay informed.

## Should

- **US-5.06** — As a **paralegal**, I want to send messages to clients on behalf of an associate so
  that routine communications don't require attorney time.

- **US-5.07** — As a **managing partner**, I want to see all communications for an expediente in
  case an associate is unavailable so that client service continuity is maintained.

- **US-5.08** — As an **associate**, I want to use message templates for common communications
  (welcome, document request, case update, hearing reminder, case closed) so that I save time on
  repetitive messages.

- **US-5.09** — As an **associate**, I want to record phone calls and meetings as communication
  entries (date, duration, summary) so that all client interactions are tracked.

## Could

- **US-5.10** — As a **client**, I want to receive notifications via email AND in-app so that I
  don't miss anything regardless of how I access the system.

- **US-5.11** — As an **associate**, I want to see a communication summary per client across all
  their expedientes so that I have the full relationship picture.

- **US-5.12** — As a **managing partner**, I want to see response time metrics (time to first
  response, avg. response time) so that I can ensure client service quality.

## Summary

| Priority  | Count  |
| --------- | :----: |
| Must      |   5    |
| Should    |   4    |
| Could     |   3    |
| **Total** | **12** |
```

**Step 2: Verify & Commit**

```bash
npx markdownlint-cli2 "docs/analysis/user-stories/us-ep05-client-communication.md"
git add docs/analysis/user-stories/us-ep05-client-communication.md
git commit -m "docs(user-stories): add EP-05 client communication (12 stories)"
```

---

## Task 9: User Stories — EP-06 Client Portal

**Files:**

- Create: `docs/analysis/user-stories/us-ep06-client-portal.md`

**Step 1: Write EP-06 stories**

```markdown
# EP-06: Client Portal (Portal de Clientes)

The client-facing view of the system. Minimal, focused, and designed for infrequent and
non-technical users. Provides case status visibility, document management, communication, and
invoice access.

## Must (UMFP)

- **US-6.01** — As a **client**, I want to log in with a simple method (magic link or email +
  password) so that I can access my information without complex credentials.

- **US-6.02** — As a **client**, I want to see a dashboard with my active expedientes, pending
  documents, upcoming hearings, and unread messages so that I have everything at a glance.

- **US-6.03** — As a **client**, I want to see only my own expedientes and documents so that my
  information is private from other clients.

- **US-6.04** — As a **client**, I want to see the current status and procedural phase of my case(s)
  without having to call the firm so that I stay informed.

- **US-6.05** — As a **client**, I want to view and download documents that my lawyer has shared
  with me (sentencias, escritos, informes) so that I have copies of everything relevant.

## Should

- **US-6.06** — As a **client**, I want to see upcoming hearing dates and what I need to prepare so
  that I can participate effectively.

- **US-6.07** — As a **client**, I want to see a history of past expedientes (closed/archived) so
  that I can reference previous work done by the firm.

- **US-6.08** — As a **client**, I want to update my contact information (phone, email, address) so
  that the firm always has my current details.

- **US-6.09** — As a **client**, I want to view my invoices and payment status so that I know what I
  owe and what I've paid.

## Could

- **US-6.10** — As a **client**, I want to rate the service received on a closed expediente so that
  the firm can measure client satisfaction.

- **US-6.11** — As a **client**, I want to digitally sign the hoja de encargo profesional through
  the portal so that I don't need to visit the office.

- **US-6.12** — As a **client**, I want to see a breakdown of costs (honorarios, suplidos,
  provisiones de fondos) so that I understand what I'm paying for.

## Summary

| Priority  | Count  |
| --------- | :----: |
| Must      |   5    |
| Should    |   4    |
| Could     |   3    |
| **Total** | **12** |
```

**Step 2: Verify & Commit**

```bash
npx markdownlint-cli2 "docs/analysis/user-stories/us-ep06-client-portal.md"
git add docs/analysis/user-stories/us-ep06-client-portal.md
git commit -m "docs(user-stories): add EP-06 client portal (12 stories)"
```

---

## Task 10: User Stories — EP-07 Appointment Scheduling

**Files:**

- Create: `docs/analysis/user-stories/us-ep07-appointment-scheduling.md`

**Step 1: Write EP-07 stories**

```markdown
# EP-07: Appointment Scheduling (Agenda de Citas)

Scheduling and managing client appointments: consultations, follow-ups, document signings. Distinct
from court hearings (EP-03) which are part of the proceeding workflow.

## Must (UMFP)

- **US-7.01** — As an **associate**, I want to schedule an appointment with a client for a specific
  date, time, and type (consultation, follow-up, signing) so that both parties know when to meet.

- **US-7.02** — As a **client**, I want to receive a notification about my upcoming appointment with
  date, time, and location (office or remote) so that I don't forget.

- **US-7.03** — As an **associate**, I want to see my upcoming appointments in a calendar view so
  that I can plan my day.

- **US-7.04** — As a **paralegal**, I want to schedule appointments on behalf of an associate so
  that I can coordinate their calendar.

- **US-7.05** — As a **client**, I want to confirm or request to reschedule an appointment so that
  conflicts are handled early.

- **US-7.06** — As an **associate**, I want to receive a notification when a client requests a
  reschedule so that I can propose a new time.

## Should

- **US-7.07** — As an **associate**, I want to see my availability and avoid double-bookings so that
  scheduling conflicts don't happen.

- **US-7.08** — As a **client**, I want to receive reminders (24h, 1h before) about upcoming
  appointments so that I don't miss them.

- **US-7.09** — As a **paralegal**, I want to see a combined calendar for all associates I support
  so that I can coordinate across multiple schedules.

- **US-7.10** — As an **associate**, I want to link appointments to specific expedientes so that the
  meeting context is clear.

## Could

- **US-7.11** — As an **associate**, I want to define recurring appointments (e.g., weekly check-in
  with a long-running client) so that I don't recreate them manually.

- **US-7.12** — As an **office admin**, I want to see room/resource availability when scheduling
  in-person meetings so that we don't double-book meeting rooms.

## Summary

| Priority  | Count  |
| --------- | :----: |
| Must      |   6    |
| Should    |   4    |
| Could     |   2    |
| **Total** | **12** |
```

**Step 2: Verify & Commit**

```bash
npx markdownlint-cli2 "docs/analysis/user-stories/us-ep07-appointment-scheduling.md"
git add docs/analysis/user-stories/us-ep07-appointment-scheduling.md
git commit -m "docs(user-stories): add EP-07 appointment scheduling (12 stories)"
```

---

## Task 11: User Stories — EP-08 Financial Management

**Files:**

- Create: `docs/analysis/user-stories/us-ep08-financial-management.md`

**Step 1: Write EP-08 stories**

```markdown
# EP-08: Financial Management (Gestión Económica)

Time tracking, billing, and invoicing for a Spanish law firm. Includes Spanish-specific financial
concepts: provisiones de fondos (advance payments), suplidos (third-party pass-through expenses not
subject to IVA), VeriFactu-compliant invoicing (mandatory by July 2026), and IRPF retention
calculations.

## Must (UMFP)

- **US-8.01** — As an **associate**, I want to track my billable time with a start/stop timer linked
  to specific expedientes so that I capture all billable work accurately.

- **US-8.02** — As an **associate**, I want to manually enter time entries with date, duration,
  description, and expediente so that I can log work done without the timer.

- **US-8.03** — As a **paralegal**, I want to generate invoices (notas de honorarios) from time
  entries and expenses for a specific expediente so that billing is accurate.

- **US-8.04** — As a **managing partner**, I want invoices to comply with VeriFactu requirements
  (sequential numbering, QR code, AEAT reporting) so that the firm meets its legal obligations by
  the July 2026 deadline.

- **US-8.05** — As a **paralegal**, I want to track payments received and mark invoices as paid,
  partial, or overdue so that collection status is always current.

- **US-8.06** — As an **associate**, I want to record provisiones de fondos (advance payments)
  linked to expedientes so that client advances are properly accounted for.

- **US-8.07** — As an **associate**, I want to record suplidos (third-party expenses: tasas
  judiciales, gastos notariales, gastos registrales) separately from honorarios so that they are
  invoiced correctly (suplidos are not subject to IVA).

## Should

- **US-8.08** — As a **managing partner**, I want to see aged receivables (facturas pendientes) with
  aging buckets (30, 60, 90+ days) so that I can manage collections.

- **US-8.09** — As a **managing partner**, I want to approve invoices before they are sent to
  clients so that billing is reviewed.

- **US-8.10** — As a **managing partner**, I want profitability reports by expediente, by client,
  and by area of law so that I can evaluate which work is profitable.

- **US-8.11** — As an **associate**, I want to define the billing model for each expediente (hourly,
  fixed fee, iguala/retainer, mixed, cuota litis) so that invoicing matches the engagement terms.

- **US-8.12** — As an **associate**, I want the system to apply the correct IVA (21%) and IRPF
  retention (15% standard, 7% first 3 years) to invoices automatically so that tax calculations are
  accurate.

- **US-8.13** — As a **managing partner**, I want to see each lawyer's billable hours, utilization
  rate, and realization rate so that I can evaluate performance.

## Could

- **US-8.14** — As a **paralegal**, I want to calculate tasación de costas using the applicable
  baremo from the Colegio de Abogados so that recoverable costs are properly quantified after a
  favorable judgment.

- **US-8.15** — As a **managing partner**, I want to track turno de oficio designations and pending
  compensation from the Comunidad Autónoma so that legal aid work is properly managed.

- **US-8.16** — As an **associate**, I want to generate proforma invoices (minutas) before the final
  invoice so that clients can review before billing.

- **US-8.17** — As a **managing partner**, I want to see revenue trends (monthly, quarterly, yearly)
  by lawyer and by area of law so that I can track firm performance over time.

- **US-8.18** — As a **paralegal**, I want to send payment reminders to clients with overdue
  invoices so that collections are pursued systematically.

## Summary

| Priority  | Count  |
| --------- | :----: |
| Must      |   7    |
| Should    |   6    |
| Could     |   5    |
| **Total** | **18** |
```

**Step 2: Verify & Commit**

```bash
npx markdownlint-cli2 "docs/analysis/user-stories/us-ep08-financial-management.md"
git add docs/analysis/user-stories/us-ep08-financial-management.md
git commit -m "docs(user-stories): add EP-08 financial management (18 stories)"
```

---

## Task 12: User Stories — EP-09 Dashboard & Reporting

**Files:**

- Create: `docs/analysis/user-stories/us-ep09-dashboard-reporting.md`

**Step 1: Write EP-09 stories**

```markdown
# EP-09: Dashboard & Reporting (Dashboard y Reporting)

Overview and analytics for firm management. Partner-level KPIs, associate personal dashboards,
workload distribution, and exportable reports.

## Must (UMFP)

- **US-9.01** — As a **managing partner**, I want a dashboard with: total active expedientes,
  approaching deadlines, overdue documents, today's hearings, and pending invoices so that I have an
  operational snapshot.

- **US-9.02** — As an **associate**, I want a personal dashboard with: my active expedientes,
  upcoming deadlines, pending document requests, today's appointments, and unbilled time so that I
  can plan my day.

- **US-9.03** — As a **managing partner**, I want to see workload distribution across associates
  (expedientes per associate, by status, by area of law) so that I can rebalance when needed.

## Should

- **US-9.04** — As a **managing partner**, I want to see deadline compliance metrics (% met on time,
  average margin, missed deadlines) per associate so that I can monitor quality.

- **US-9.05** — As an **office admin**, I want to export reports (CSV/PDF) of expediente activity,
  billing, and deadlines for a given period so that I can produce management reports.

- **US-9.06** — As a **managing partner**, I want to see client satisfaction metrics (ratings,
  response times, resolution times) so that I can improve service quality.

- **US-9.07** — As a **managing partner**, I want to see case outcome statistics (estimado,
  desestimado, parcialmente estimado, acuerdo) by area of law and by associate so that I can
  identify strengths.

## Could

- **US-9.08** — As a **managing partner**, I want to see trend charts (expedientes opened/closed per
  month, avg. resolution time over time) so that I can track firm performance.

- **US-9.09** — As a **managing partner**, I want a pipeline view of potential new expedientes
  (consultas iniciales, presupuestos enviados, hojas de encargo firmadas) so that I can forecast
  workload.

- **US-9.10** — As a **managing partner**, I want to see financial KPIs (revenue, average matter
  value, collection rate, realization rate) so that I can monitor firm health.

- **US-9.11** — As an **associate**, I want to see my personal performance metrics (billable hours,
  utilization, expedientes resolved) so that I can track my own progress.

- **US-9.12** — As an **office admin**, I want to schedule automatic reports (weekly, monthly) sent
  to managing partners so that reporting is consistent.

## Summary

| Priority  | Count  |
| --------- | :----: |
| Must      |   3    |
| Should    |   4    |
| Could     |   5    |
| **Total** | **12** |
```

**Step 2: Verify & Commit**

```bash
npx markdownlint-cli2 "docs/analysis/user-stories/us-ep09-dashboard-reporting.md"
git add docs/analysis/user-stories/us-ep09-dashboard-reporting.md
git commit -m "docs(user-stories): add EP-09 dashboard & reporting (12 stories)"
```

---

## Task 13: User Stories — EP-10 Administration & Security

**Files:**

- Create: `docs/analysis/user-stories/us-ep10-administration-security.md`

**Step 1: Write EP-10 stories**

```markdown
# EP-10: Administration & Security (Administración y Seguridad)

Firm configuration, user management, client registry, court directories, RGPD compliance, and audit
trails. The operational foundation that supports all other epics.

## Must (UMFP)

- **US-10.01** — As an **office admin**, I want to register the firm's information (name, CIF,
  address, phone, email, sector) so that the system is properly configured.

- **US-10.02** — As an **office admin**, I want to create user accounts with roles (socio director,
  asociado, paralegal, admin) and set permissions so that access is appropriate to each role.

- **US-10.03** — As an **office admin**, I want to manage the client registry (add, edit,
  deactivate) with NIF/CIF, contact information, and type (individual, company) so that client data
  is centralized.

- **US-10.04** — As an **associate**, I want all my actions to be recorded in an audit trail (who,
  what, when) so that there is a complete record for compliance and dispute resolution.

- **US-10.05** — As a **managing partner**, I want role-based access control so that associates only
  see their expedientes while partners see all, and clients only see their own information.

## Should

- **US-10.06** — As an **office admin**, I want to maintain a directory of juzgados (name, type,
  jurisdiction, address, partido judicial) so that court information is centralized and accurate.

- **US-10.07** — As an **office admin**, I want to maintain a directory of procuradores (name,
  colegio, número de colegiado, contact, partidos judiciales) so that assignments are efficient.

- **US-10.08** — As a **managing partner**, I want to configure RGPD compliance settings (data
  retention periods, consent management, data processing register) so that the firm meets its legal
  obligations.

- **US-10.09** — As an **office admin**, I want to import clients from a CSV/Excel file so that I
  can migrate data from our previous system.

- **US-10.10** — As an **office admin**, I want to configure notification preferences (email
  settings, notification templates) so that communications are properly branded.

## Could

- **US-10.11** — As a **managing partner**, I want to assign client portfolios to associates so that
  each lawyer has a defined set of clients they are responsible for.

- **US-10.12** — As a **managing partner**, I want to configure the firm's areas of practice (civil,
  penal, laboral, mercantil, etc.) with default matter types so that expediente classification is
  standardized.

- **US-10.13** — As an **office admin**, I want to configure the judicial calendar (national
  holidays, August recess, autonomous community holidays) so that deadline calculations are
  accurate.

- **US-10.14** — As a **managing partner**, I want to track license usage and system health so that
  I can ensure the platform is running properly.

## Summary

| Priority  | Count  |
| --------- | :----: |
| Must      |   5    |
| Should    |   5    |
| Could     |   4    |
| **Total** | **14** |
```

**Step 2: Verify & Commit**

```bash
npx markdownlint-cli2 "docs/analysis/user-stories/us-ep10-administration-security.md"
git add docs/analysis/user-stories/us-ep10-administration-security.md
git commit -m "docs(user-stories): add EP-10 administration & security (14 stories)"
```

---

## Task 14: Update Domain Dictionary

**Files:**

- Modify: `docs/analysis/domain-dictionary.md`

**Step 1: Rewrite domain dictionary with legal terms**

Rewrite the file with the following structure. Keep the existing format (tables with English code /
Spanish UI) but add all legal domain terms.

### Sections to include

**1. Scope note** — Add a note at the top: "Scope: Legal vertical only (see ADR-0005). Fiscal and
labor terms not included."

**2. Core entities** — Update table:

| English (Code)   | Spanish (UI)            | Description                                               |
| ---------------- | ----------------------- | --------------------------------------------------------- |
| Firm             | Despacho                | The tenant. A law firm                                    |
| Member           | Miembro                 | User belonging to the firm                                |
| Client           | Cliente                 | External person/entity receiving services                 |
| Expediente       | Expediente              | Central work unit — case dossier (same in both languages) |
| Proceeding       | Procedimiento           | Judicial proceeding within an expediente                  |
| ProceduralAction | Actuación Procesal      | Event in a proceeding (filing, hearing, ruling)           |
| Deadline         | Plazo                   | Procedural or internal deadline                           |
| Hearing          | Vista / Señalamiento    | Scheduled court hearing                                   |
| Court            | Juzgado                 | Court of law                                              |
| CourtAgent       | Procurador              | Court representative                                      |
| Party            | Parte Procesal          | Person/entity with a role in a proceeding                 |
| Resolution       | Resolución Judicial     | Court decision (sentencia, auto, providencia)             |
| DocumentRequest  | Solicitud de Documentos | Request for documents from a client                       |
| Document         | Documento               | Uploaded or generated file                                |
| Appointment      | Cita                    | Scheduled meeting (not a court hearing)                   |
| Message          | Mensaje                 | Communication within an expediente                        |
| Notification     | Notificación            | System alert for members                                  |
| TimeEntry        | Registro de Tiempo      | Billable time entry                                       |
| Invoice          | Factura                 | Client invoice                                            |
| User             | Usuario                 | Authentication identity                                   |
| Token            | Token                   | Auth refresh token                                        |

**3. Enums** — Update/add:

**Member roles:** `managing_partner` (Socio Director), `associate` (Abogado Asociado), `paralegal`
(Auxiliar Jurídico), `office_admin` (Administrador)

**Expediente status:** `open` (Abierto), `in_progress` (En Trámite), `pending_resolution` (Pendiente
Resolución), `closed` (Cerrado), `archived` (Archivado)

**Jurisdiction:** `civil` (Civil), `criminal` (Penal), `labor` (Laboral/Social), `administrative`
(Contencioso-Administrativo), `commercial` (Mercantil)

**Matter type (civil):** `ordinary` (Ordinario), `verbal` (Verbal), `injunction` (Monitorio),
`execution` (Ejecución), `voluntary_jurisdiction` (Jurisdicción Voluntaria), `interim_measures`
(Medidas Cautelares)

**Procedural phase:** `filing` (Interposición), `admission` (Admisión), `discovery` (Instrucción),
`preliminary_hearing` (Audiencia Previa), `trial` (Juicio Oral), `sentencing` (Sentencia), `appeal`
(Recurso), `execution` (Ejecución)

**Resolution type:** `sentencia` (Sentencia), `auto` (Auto), `providencia` (Providencia), `decreto`
(Decreto)

**Party role:** `plaintiff` (Demandante), `defendant` (Demandado), `co_defendant` (Codemandado),
`third_party` (Tercero Interviniente), `private_prosecutor` (Acusación Particular), `accused`
(Acusado)

**Deadline type:** `procedural` (Procesal), `prescription` (Prescripción), `expiration` (Caducidad),
`internal` (Interno)

**Deadline consequence:** `preclusion` (Preclusión), `case_abandonment` (Caducidad de la Instancia),
`prescription_expiry` (Prescripción), `none` (Ninguna)

**Hearing type:** `preliminary_hearing` (Audiencia Previa), `main_hearing` (Vista Principal),
`oral_trial` (Juicio Oral), `appearance` (Comparecencia), `ratification` (Ratificación)

**Appointment type:** `consultation` (Consulta), `follow_up` (Seguimiento), `signing` (Firma)

**Invoice type:** `fee_note` (Nota de Honorarios), `final_invoice` (Factura Definitiva), `proforma`
(Proforma/Minuta), `advance_payment` (Provisión de Fondos), `expenses_note` (Nota de Suplidos)

**Billing model:** `hourly` (Por Horas), `fixed_fee` (Precio Cerrado), `retainer` (Iguala), `mixed`
(Mixto), `success_fee` (Cuota Litis)

**4. Legal-specific institutional terms** — New section:

| Term                | Description                                                        |
| ------------------- | ------------------------------------------------------------------ |
| LexNET              | Electronic judicial communications platform (mandatory)            |
| CTEAJE              | Catálogo de Entidades de la Administración Judicial Electrónica    |
| VeriFactu           | Electronic invoicing system (mandatory by July 2026)               |
| Hoja de Encargo     | Professional engagement letter                                     |
| Provisión de Fondos | Advance payment (subject to IVA, requires invoice)                 |
| Suplidos            | Third-party expenses paid on behalf of client (NOT subject to IVA) |
| Tasación de Costas  | Formal calculation of recoverable court costs                      |
| Turno de Oficio     | Legal aid roster managed by Colegio de Abogados                    |
| Número de Colegiado | Bar registration number                                            |
| Partido Judicial    | Judicial district                                                  |
| Días Hábiles        | Working days (excluding weekends, holidays, August)                |
| Cuota Litis         | Success/contingency fee                                            |

**5. Naming conventions** — Keep existing 8 rules, no changes needed.

**Step 2: Verify & Commit**

```bash
npx markdownlint-cli2 "docs/analysis/domain-dictionary.md"
git add docs/analysis/domain-dictionary.md
git commit -m "docs(domain-dictionary): rewrite with comprehensive legal domain terms"
```

---

## Task 15: Rewrite Data Model

**Files:**

- Modify: `docs/architecture/data-model/data-model.md`

**Step 1: Rewrite data model with legal entities**

Expand from 9 to 21 entities. Keep the same format (entity tables with Attribute, Type, Required,
Description columns). Keep the design principles section but update it.

### Design principles update

Add to existing principles:

- **Legal-vertical scope**: This model targets law firm (despacho) workflows per ADR-0005. Fiscal
  and labor entities are out of scope.
- **Expediente as central organizer**: Replaces the generic "Matter" entity. All work revolves
  around expedientes.
- **Proceeding chain**: An expediente may contain multiple proceedings linked sequentially (first
  instance → appeal → cassation) via `previousProceedingId`.

### Entity map (updated)

```
Firm (tenant)
  ├── Member (firm users: associates, paralegals, partners, admins)
  ├── Client
  ├── Court (juzgado directory)
  ├── CourtAgent (procurador directory)
  ├── Expediente (central work unit — replaces Matter)
  │     ├── Party (parties with procedural roles)
  │     ├── Proceeding (procedimientos within the expediente)
  │     │     ├── ProceduralAction (actuaciones procesales)
  │     │     ├── Resolution (resoluciones judiciales)
  │     │     ├── Hearing (vistas/señalamientos)
  │     │     └── Deadline (plazos procesales)
  │     ├── DocumentRequest → Document (uploaded files)
  │     ├── Appointment (client meetings)
  │     ├── Message (communication thread)
  │     ├── TimeEntry (billable time)
  │     └── Invoice (billing)
  └── Notification (per member, event-driven)

Auth:
  User (1:1 with Member or Client)
  Token (refresh tokens)
```

**Total: 21 stored entities** (up from 9).

### New/changed entity definitions

The following entities need full attribute tables. Keep existing entities (Firm, Member, Client,
DocumentRequest, Document, Appointment, Message, Notification, User, Token) but update where needed.
Add new entities:

**Expediente** (replaces Matter):

| Attribute    | Type        | Required | Description                                                       |
| ------------ | ----------- | :------: | ----------------------------------------------------------------- |
| id           | UUIDv7 (PK) |   Yes    |                                                                   |
| firmId       | UUIDv7 (FK) |   Yes    | Belongs to Firm                                                   |
| clientId     | UUIDv7 (FK) |   Yes    | Primary client                                                    |
| assigneeId   | UUIDv7 (FK) |   Yes    | Lead associate (Member)                                           |
| reference    | Text        |   Yes    | Internal reference (e.g., "2026/0042")                            |
| title        | Text        |   Yes    | Brief descriptive title                                           |
| description  | Text        |    No    | Detailed description                                              |
| jurisdiction | Enum        |   Yes    | `civil`, `criminal`, `labor`, `administrative`, `commercial`      |
| matterType   | Text        |    No    | Subtype (e.g., "ordinario", "verbal", "monitorio")                |
| status       | Enum        |   Yes    | `open`, `in_progress`, `pending_resolution`, `closed`, `archived` |
| courtId      | UUIDv7 (FK) |    No    | Assigned court                                                    |
| courtAgentId | UUIDv7 (FK) |    No    | Assigned procurador                                               |
| caseNumber   | Text        |    No    | Court case number (número de autos)                               |
| amount       | Decimal     |    No    | Amount in dispute (cuantía)                                       |
| billingModel | Enum        |    No    | `hourly`, `fixed_fee`, `retainer`, `mixed`, `success_fee`         |
| parentId     | UUIDv7 (FK) |    No    | Parent expediente (for related cases)                             |
| createdAt    | DateTime    |   Yes    |                                                                   |
| updatedAt    | DateTime    |   Yes    |                                                                   |

**Court** (Juzgado):

| Attribute        | Type        | Required | Description                                                          |
| ---------------- | ----------- | :------: | -------------------------------------------------------------------- |
| id               | UUIDv7 (PK) |   Yes    |                                                                      |
| firmId           | UUIDv7 (FK) |   Yes    | Belongs to Firm (firm-scoped directory)                              |
| name             | Text        |   Yes    | Full name (e.g., "Juzgado de Primera Instancia n.3 de Madrid")       |
| type             | Text        |   Yes    | Court type (Primera Instancia, Instrucción, Mercantil, Social, etc.) |
| jurisdiction     | Enum        |   Yes    | `civil`, `criminal`, `labor`, `administrative`                       |
| judicialDistrict | Text        |   Yes    | Partido judicial                                                     |
| province         | Text        |   Yes    | Province                                                             |
| address          | Text        |    No    | Physical address                                                     |
| phone            | Text        |    No    | Contact phone                                                        |
| email            | Text        |    No    | Contact email                                                        |
| active           | Boolean     |   Yes    | Default: true                                                        |
| createdAt        | DateTime    |   Yes    |                                                                      |
| updatedAt        | DateTime    |   Yes    |                                                                      |

**CourtAgent** (Procurador):

| Attribute         | Type        | Required | Description                            |
| ----------------- | ----------- | :------: | -------------------------------------- |
| id                | UUIDv7 (PK) |   Yes    |                                        |
| firmId            | UUIDv7 (FK) |   Yes    | Belongs to Firm                        |
| firstName         | Text        |   Yes    |                                        |
| lastName          | Text        |   Yes    |                                        |
| barNumber         | Text        |    No    | Número de colegiado                    |
| barAssociation    | Text        |    No    | Colegio de Procuradores                |
| judicialDistricts | Text[]      |    No    | Partidos judiciales where they operate |
| phone             | Text        |    No    |                                        |
| email             | Text        |    No    |                                        |
| active            | Boolean     |   Yes    | Default: true                          |
| createdAt         | DateTime    |   Yes    |                                        |
| updatedAt         | DateTime    |   Yes    |                                        |

**Party** (Parte Procesal):

| Attribute          | Type        | Required | Description                                                   |
| ------------------ | ----------- | :------: | ------------------------------------------------------------- |
| id                 | UUIDv7 (PK) |   Yes    |                                                               |
| expedienteId       | UUIDv7 (FK) |   Yes    | Belongs to Expediente                                         |
| name               | Text        |   Yes    | Party name (may not be a Client in the system)                |
| role               | Enum        |   Yes    | `plaintiff`, `defendant`, `co_defendant`, `third_party`, etc. |
| clientId           | UUIDv7 (FK) |    No    | If the party is also a Client                                 |
| taxId              | Text        |    No    | NIF/CIF                                                       |
| opposingCounsel    | Text        |    No    | Name of opposing lawyer                                       |
| opposingCourtAgent | Text        |    No    | Name of opposing procurador                                   |
| createdAt          | DateTime    |   Yes    |                                                               |

**Proceeding** (Procedimiento):

| Attribute            | Type        | Required | Description                                                                                             |
| -------------------- | ----------- | :------: | ------------------------------------------------------------------------------------------------------- |
| id                   | UUIDv7 (PK) |   Yes    |                                                                                                         |
| expedienteId         | UUIDv7 (FK) |   Yes    | Belongs to Expediente                                                                                   |
| type                 | Text        |   Yes    | Tipo de procedimiento (ordinario, verbal, monitorio, etc.)                                              |
| caseNumber           | Text        |    No    | Court case number for this specific proceeding                                                          |
| courtId              | UUIDv7 (FK) |    No    | Court for this proceeding (may differ from expediente)                                                  |
| phase                | Enum        |   Yes    | `filing`, `admission`, `discovery`, `preliminary_hearing`, `trial`, `sentencing`, `appeal`, `execution` |
| previousProceedingId | UUIDv7 (FK) |    No    | Links to prior instance (appeal chain)                                                                  |
| filingDate           | Date        |    No    | When the proceeding was filed                                                                           |
| resolutionDate       | Date        |    No    | When resolved                                                                                           |
| outcome              | Text        |    No    | Result if resolved                                                                                      |
| createdAt            | DateTime    |   Yes    |                                                                                                         |
| updatedAt            | DateTime    |   Yes    |                                                                                                         |

**ProceduralAction** (Actuación Procesal):

| Attribute        | Type        | Required | Description                                             |
| ---------------- | ----------- | :------: | ------------------------------------------------------- |
| id               | UUIDv7 (PK) |   Yes    |                                                         |
| proceedingId     | UUIDv7 (FK) |   Yes    | Belongs to Proceeding                                   |
| type             | Text        |   Yes    | demanda, contestación, prueba, sentencia, recurso, etc. |
| date             | Date        |   Yes    | When the action occurred                                |
| notificationDate | Date        |    No    | When notified (for court actions)                       |
| description      | Text        |    No    | Description of the action                               |
| responsibleId    | UUIDv7 (FK) |    No    | Assigned member                                         |
| status           | Enum        |   Yes    | `pending`, `completed`, `overdue`                       |
| createdAt        | DateTime    |   Yes    |                                                         |

**Resolution** (Resolución Judicial):

| Attribute          | Type        | Required | Description                                   |
| ------------------ | ----------- | :------: | --------------------------------------------- |
| id                 | UUIDv7 (PK) |   Yes    |                                               |
| proceedingId       | UUIDv7 (FK) |   Yes    | Belongs to Proceeding                         |
| type               | Enum        |   Yes    | `sentencia`, `auto`, `providencia`, `decreto` |
| date               | Date        |   Yes    | Date issued                                   |
| notificationDate   | Date        |    No    | Date notified                                 |
| ruling             | Text        |    No    | The fallo / parte dispositiva                 |
| isFinal            | Boolean     |   Yes    | Whether firme (res judicata)                  |
| isAppealable       | Boolean     |   Yes    | Whether recurrible                            |
| appealType         | Text        |    No    | Available appeal type                         |
| appealDeadlineDays | Integer     |    No    | Days to file appeal                           |
| documentId         | UUIDv7 (FK) |    No    | Full text document                            |
| createdAt          | DateTime    |   Yes    |                                               |

**Hearing** (Vista / Señalamiento):

| Attribute         | Type        | Required | Description                                                                       |
| ----------------- | ----------- | :------: | --------------------------------------------------------------------------------- |
| id                | UUIDv7 (PK) |   Yes    |                                                                                   |
| proceedingId      | UUIDv7 (FK) |   Yes    | Belongs to Proceeding                                                             |
| type              | Enum        |   Yes    | `preliminary_hearing`, `main_hearing`, `oral_trial`, `appearance`, `ratification` |
| dateTime          | DateTime    |   Yes    | Scheduled date and time                                                           |
| courtId           | UUIDv7 (FK) |    No    | Court (may differ from proceeding court)                                          |
| courtroom         | Text        |    No    | Sala number                                                                       |
| estimatedDuration | Integer     |    No    | Minutes                                                                           |
| status            | Enum        |   Yes    | `scheduled`, `completed`, `suspended`, `postponed`                                |
| outcome           | Text        |    No    | Result summary                                                                    |
| notes             | Text        |    No    | Preparation or outcome notes                                                      |
| createdAt         | DateTime    |   Yes    |                                                                                   |
| updatedAt         | DateTime    |   Yes    |                                                                                   |

**Deadline** (Plazo):

| Attribute          | Type        | Required | Description                                                     |
| ------------------ | ----------- | :------: | --------------------------------------------------------------- |
| id                 | UUIDv7 (PK) |   Yes    |                                                                 |
| expedienteId       | UUIDv7 (FK) |   Yes    | Belongs to Expediente                                           |
| proceedingId       | UUIDv7 (FK) |    No    | Optionally linked to a specific proceeding                      |
| type               | Enum        |   Yes    | `procedural`, `prescription`, `expiration`, `internal`          |
| description        | Text        |   Yes    | What must be done                                               |
| startDate          | Date        |   Yes    | When deadline starts counting                                   |
| dueDate            | Date        |   Yes    | Calculated due date                                             |
| workingDays        | Boolean     |   Yes    | Whether counted in días hábiles                                 |
| durationDays       | Integer     |   Yes    | Number of days (for calculation audit)                          |
| consequence        | Enum        |    No    | `preclusion`, `case_abandonment`, `prescription_expiry`, `none` |
| responsibleId      | UUIDv7 (FK) |   Yes    | Assigned member                                                 |
| status             | Enum        |   Yes    | `pending`, `completed`, `overdue`                               |
| completedAt        | DateTime    |    No    | When marked as done                                             |
| completionEvidence | Text        |    No    | Proof (e.g., LexNET receipt number)                             |
| createdAt          | DateTime    |   Yes    |                                                                 |
| updatedAt          | DateTime    |   Yes    |                                                                 |

**TimeEntry** (Registro de Tiempo):

| Attribute       | Type        | Required | Description                    |
| --------------- | ----------- | :------: | ------------------------------ |
| id              | UUIDv7 (PK) |   Yes    |                                |
| expedienteId    | UUIDv7 (FK) |   Yes    | Linked to expediente           |
| memberId        | UUIDv7 (FK) |   Yes    | Who worked                     |
| date            | Date        |   Yes    | Work date                      |
| durationMinutes | Integer     |   Yes    | Duration in minutes            |
| description     | Text        |   Yes    | Activity description           |
| billable        | Boolean     |   Yes    | Default: true                  |
| hourlyRate      | Decimal     |    No    | Rate applied (for calculation) |
| invoiceId       | UUIDv7 (FK) |    No    | If already invoiced            |
| createdAt       | DateTime    |   Yes    |                                |

**Invoice** (Factura):

| Attribute     | Type        | Required | Description                                                                 |
| ------------- | ----------- | :------: | --------------------------------------------------------------------------- |
| id            | UUIDv7 (PK) |   Yes    |                                                                             |
| firmId        | UUIDv7 (FK) |   Yes    | Belongs to Firm                                                             |
| clientId      | UUIDv7 (FK) |   Yes    | Billed client                                                               |
| expedienteId  | UUIDv7 (FK) |    No    | Linked expediente (optional for general invoices)                           |
| invoiceNumber | Text        |   Yes    | Sequential number (VeriFactu compliant)                                     |
| type          | Enum        |   Yes    | `fee_note`, `final_invoice`, `proforma`, `advance_payment`, `expenses_note` |
| issueDate     | Date        |   Yes    |                                                                             |
| concept       | Text        |   Yes    | Description of services                                                     |
| taxableBase   | Decimal     |   Yes    | Base imponible                                                              |
| vatRate       | Decimal     |   Yes    | IVA rate (21% standard)                                                     |
| vatAmount     | Decimal     |   Yes    | IVA amount                                                                  |
| irpfRate      | Decimal     |    No    | IRPF retention rate (15% or 7%)                                             |
| irpfAmount    | Decimal     |    No    | IRPF amount                                                                 |
| totalAmount   | Decimal     |   Yes    | Total                                                                       |
| paymentStatus | Enum        |   Yes    | `unpaid`, `partial`, `paid`, `overdue`                                      |
| paidAt        | DateTime    |    No    |                                                                             |
| verifactuQr   | Text        |    No    | VeriFactu QR code data                                                      |
| createdAt     | DateTime    |   Yes    |                                                                             |
| updatedAt     | DateTime    |   Yes    |                                                                             |

### Updated entities

**Expediente status transitions:**

```
open → in_progress → pending_resolution → closed → archived
```

**Member** — Add `barNumber` (número de colegiado) and `barAssociation` (colegio de abogados)
attributes.

**Appointment** — Update `type` enum to `consultation`, `follow_up`, `signing` (remove hearing types
which are now in the Hearing entity). Update `location` to `office`, `remote`, `client_site`. Remove
`matterId`, add `expedienteId`.

### Updated relationships

```
Firm 1──N Member
Firm 1──N Client
Firm 1──N Court
Firm 1──N CourtAgent
Firm 1──N Expediente
Expediente N──1 Client
Expediente N──1 Member (assignee)
Expediente N──0..1 Court
Expediente N──0..1 CourtAgent
Expediente 0..1──N Expediente (parent-child)
Expediente 1──N Party
Expediente 1──N Proceeding
Proceeding 1──N ProceduralAction
Proceeding 1──N Resolution
Proceeding 1──N Hearing
Proceeding 0..1──N Deadline
Expediente 1──N Deadline (direct)
Expediente 1──N DocumentRequest
DocumentRequest 1──N Document
Expediente 1──N Appointment
Expediente 1──N Message
Expediente 1──N TimeEntry
Expediente 1──N Invoice
Member 1──N Notification
```

### Updated indexes

Add indexes for new entities:

| Entity     | Index                             | Purpose                    |
| ---------- | --------------------------------- | -------------------------- |
| Expediente | (firmId, status)                  | Dashboard queries          |
| Expediente | (assigneeId, status)              | Associate workload         |
| Expediente | (clientId)                        | Client portal              |
| Expediente | (jurisdiction)                    | Category filtering         |
| Proceeding | (expedienteId)                    | Proceedings per expediente |
| Deadline   | (responsibleId, status, dueDate)  | Deadline dashboard         |
| Deadline   | (expedienteId, status)            | Expediente deadlines       |
| Hearing    | (proceedingId, dateTime)          | Hearing calendar           |
| TimeEntry  | (expedienteId, memberId)          | Billing queries            |
| Invoice    | (firmId, clientId, paymentStatus) | Receivables                |
| Party      | (expedienteId)                    | Party listing              |

**Step 2: Verify & Commit**

```bash
npx markdownlint-cli2 "docs/architecture/data-model/data-model.md"
git add docs/architecture/data-model/data-model.md
git commit -m "docs(data-model): rewrite with 21 legal domain entities (up from 9)"
```

---

## Task 16: Update Feature Inventory

**Files:**

- Modify: `docs/analysis/features/feature-inventory.md`

**Step 1: Rewrite feature inventory**

Replace the current 43 features with 43 legal-domain features mapped to the new 150 user stories.
Keep the same file format.

### Feature list

| ID  | Feature                              | Epic  | Priority | Stories                         |
| --- | ------------------------------------ | ----- | -------- | ------------------------------- |
| F01 | Expediente CRUD & Lifecycle          | EP-01 | Must     | US-1.01, 1.05, 1.06             |
| F02 | Expediente Classification            | EP-01 | Must     | US-1.11, 1.19                   |
| F03 | Party Management                     | EP-01 | Must     | US-1.03, 1.16                   |
| F04 | Court & Case Number Assignment       | EP-01 | Must     | US-1.04                         |
| F05 | Procurador Assignment                | EP-01 | Must     | US-1.02                         |
| F06 | Conflict of Interest Check           | EP-01 | Must     | US-1.09                         |
| F07 | Expediente Workload View             | EP-01 | Must     | US-1.05, 1.07, 1.08, 1.10       |
| F08 | Expediente Chain Linking             | EP-01 | Should   | US-1.12                         |
| F09 | Hoja de Encargo Management           | EP-01 | Should   | US-1.13, 6.11                   |
| F10 | Expediente Audit Timeline            | EP-01 | Should   | US-1.14                         |
| F11 | Deadline Registration & Calculation  | EP-02 | Must     | US-2.01, 2.02, 2.08, 2.13       |
| F12 | Deadline Alert System                | EP-02 | Must     | US-2.03, 2.07, 2.16             |
| F13 | Deadline Calendar                    | EP-02 | Must     | US-2.04, 2.15                   |
| F14 | Deadline Dashboard & Compliance      | EP-02 | Must     | US-2.06, 2.10                   |
| F15 | Actuación Procesal Recording         | EP-03 | Must     | US-3.01, 3.02                   |
| F16 | Proceeding Management                | EP-03 | Must     | US-3.08, 3.11, 3.12             |
| F17 | Hearing Scheduling & Coordination    | EP-03 | Must     | US-3.03, 3.04, 3.07             |
| F18 | Resolution Tracking                  | EP-03 | Must     | US-3.05, 3.06                   |
| F19 | Hearing Preparation Checklist        | EP-03 | Should   | US-3.09, 3.10                   |
| F20 | Court Directory                      | EP-03 | Should   | US-3.13, 10.06                  |
| F21 | Document Request & Collection        | EP-04 | Must     | US-4.01, 4.02, 4.04, 4.07       |
| F22 | Document Upload & Review             | EP-04 | Must     | US-4.03, 4.05, 4.06             |
| F23 | Document Organization                | EP-04 | Must     | US-4.08, 4.13                   |
| F24 | Document Templates & Generation      | EP-04 | Should   | US-4.10, 4.12                   |
| F25 | Client Messaging                     | EP-05 | Must     | US-5.01, 5.02, 5.03, 5.04       |
| F26 | Case Update Notifications            | EP-05 | Must     | US-5.05, 5.10                   |
| F27 | Message Templates                    | EP-05 | Should   | US-5.08                         |
| F28 | Client Portal Dashboard              | EP-06 | Must     | US-6.01, 6.02, 6.03, 6.04       |
| F29 | Client Portal Documents              | EP-06 | Must     | US-6.05, 4.03                   |
| F30 | Client Portal Invoices               | EP-06 | Should   | US-6.09, 6.12                   |
| F31 | Appointment Scheduling               | EP-07 | Must     | US-7.01, 7.02, 7.04, 7.05, 7.06 |
| F32 | Appointment Calendar                 | EP-07 | Must     | US-7.03, 7.07, 7.09             |
| F33 | Appointment Reminders                | EP-07 | Should   | US-7.08, 7.10                   |
| F34 | Time Tracking                        | EP-08 | Must     | US-8.01, 8.02                   |
| F35 | Invoice Generation (VeriFactu)       | EP-08 | Must     | US-8.03, 8.04, 8.12             |
| F36 | Payment Tracking                     | EP-08 | Must     | US-8.05, 8.08, 8.18             |
| F37 | Provisión de Fondos & Suplidos       | EP-08 | Must     | US-8.06, 8.07                   |
| F38 | Billing Models & Profitability       | EP-08 | Should   | US-8.10, 8.11, 8.13             |
| F39 | Partner Dashboard                    | EP-09 | Must     | US-9.01, 9.03                   |
| F40 | Associate Dashboard                  | EP-09 | Must     | US-9.02                         |
| F41 | Reports & Export                     | EP-09 | Should   | US-9.04, 9.05, 9.07             |
| F42 | Firm Configuration & User Management | EP-10 | Must     | US-10.01, 10.02, 10.03, 10.05   |
| F43 | Audit Trail & RGPD                   | EP-10 | Must     | US-10.04, 10.08                 |

**Summary:** 43 features (26 Must, 13 Should, 4 Could).

Include the traceability table (Feature → Stories) and a reverse table (Story → Features).

**Step 2: Verify & Commit**

```bash
npx markdownlint-cli2 "docs/analysis/features/feature-inventory.md"
git add docs/analysis/features/feature-inventory.md
git commit -m "docs(features): rewrite feature inventory for legal domain (43 features, 150 stories)"
```

---

## Task 17: Update Screen Inventory

**Files:**

- Modify: `docs/analysis/screens/screen-inventory.md`

**Step 1: Rewrite screen inventory**

Replace current 15 screens with legal-domain screens. Keep same format (ID, name, access, features,
route, data requirements).

### Screen list (20 screens)

**Professional screens (15):**

| ID    | Screen              | Access                        | Key Features               |
| ----- | ------------------- | ----------------------------- | -------------------------- |
| S-001 | Partner Dashboard   | Managing Partner              | F39, F14                   |
| S-002 | Associate Dashboard | Associate                     | F40, F13                   |
| S-003 | Expediente List     | Associate, Paralegal, Partner | F07                        |
| S-004 | Expediente Detail   | Associate, Paralegal, Partner | F01-F06, F08, F10, F15-F18 |
| S-005 | Proceeding Timeline | Associate                     | F15, F16, F18              |
| S-006 | Deadline Calendar   | Associate, Paralegal, Partner | F13, F14                   |
| S-007 | Hearing Calendar    | Associate, Paralegal          | F17, F32                   |
| S-008 | Document Requests   | Associate, Paralegal          | F21, F22, F23              |
| S-009 | Client List         | Associate, Paralegal, Admin   | F42                        |
| S-010 | Client Detail       | Associate, Paralegal          | F42, F25                   |
| S-011 | Court Directory     | Paralegal, Admin              | F20                        |
| S-012 | Time Tracking       | Associate                     | F34                        |
| S-013 | Invoicing           | Paralegal, Partner            | F35, F36, F37              |
| S-014 | Reports             | Partner, Admin                | F41                        |
| S-015 | Firm Settings       | Admin, Partner                | F42, F43                   |

**Client portal screens (5):**

| ID    | Screen                   | Access | Key Features |
| ----- | ------------------------ | ------ | ------------ |
| S-016 | Client Login             | Client | F28          |
| S-017 | Client Dashboard         | Client | F28          |
| S-018 | Client Expediente Detail | Client | F29, F30     |
| S-019 | Client Document Upload   | Client | F22, F29     |
| S-020 | Client Messages          | Client | F25          |

For each screen, include data requirements listing the entities and attributes displayed/edited.

**Step 2: Verify & Commit**

```bash
npx markdownlint-cli2 "docs/analysis/screens/screen-inventory.md"
git add docs/analysis/screens/screen-inventory.md
git commit -m "docs(screens): rewrite screen inventory for legal domain (20 screens)"
```

---

## Task 18: Update AGENTS.md

**Files:**

- Modify: `AGENTS.md`

**Step 1: Update AGENTS.md**

Update these sections:

- **Current Phase** — Update user stories count from 55 to 150, features from 43 mapped to 150
  stories, screens from 15 to 20, data model from 9 to 21 entities. Add ADR-0005 to the ADR list.
  Add note: "Scope: Legal vertical only (ADR-0005)."

- **Epics** — Replace the 6 epics with 10 legal-focused epics:
  1. Expediente Management (11M, 6S, 3C)
  2. Procedural Deadlines & Calendar (8M, 4S, 4C)
  3. Proceedings, Hearings & Resolutions (8M, 6S, 4C)
  4. Document Collection & Management (8M, 5S, 3C)
  5. Client Communication (5M, 4S, 3C)
  6. Client Portal (5M, 4S, 3C)
  7. Appointment Scheduling (6M, 4S, 2C)
  8. Financial Management (7M, 6S, 5C)
  9. Dashboard & Reporting (3M, 4S, 5C)
  10. Administration & Security (5M, 5S, 4C)

  UMFP scope: 66 Must stories across all 10 epics.

- **Personas** — No table changes, but note legal context.

**Step 2: Verify & Commit**

```bash
npx markdownlint-cli2 "AGENTS.md" || true  # AGENTS.md may not be tracked
npx prettier --check "AGENTS.md" || true
git add AGENTS.md 2>/dev/null || true  # May be in .git/info/exclude
```

Note: AGENTS.md is in `.git/info/exclude` so git add will not track it. The update is for local
agent reference only.

---

## Task 19: Lint & Final Verification

**Files:**

- All modified/created files

**Step 1: Run full lint suite**

```bash
npx markdownlint-cli2 "**/*.md"
npx prettier --check "**/*.md" "**/*.json" "**/*.yaml" "**/*.yml"
```

Expected: 0 errors on both.

**Step 2: Verify file counts**

```bash
ls docs/analysis/user-stories/us-ep*.md | wc -l  # Expected: 10
ls docs/architecture/adrs/ADR-*.md | wc -l       # Expected: 5
```

**Step 3: Verify story counts**

Count "\*\*US-" occurrences across all story files. Expected: 150.

```bash
grep -c "^\- \*\*US-" docs/analysis/user-stories/us-ep*.md | tail -1  # Total
```

**Step 4: Final git status**

```bash
git status
git log --oneline -15
```

Verify all changes are committed with proper conventional commit messages.
