# Architecture Diagrams

Diagrams describing the Practice Desk domain model, workflows, and interactions. Each diagram uses
the shared brand palette.

## Shared Theme Files

| File                                       | Format   | Purpose                                                |
| ------------------------------------------ | -------- | ------------------------------------------------------ |
| [brand-palette.puml](brand-palette.puml)   | PlantUML | Shared skinparam include for all `.puml` diagrams      |
| [mermaid-config.json](mermaid-config.json) | JSON     | Mermaid `initialize` config with brand theme variables |

## Class Diagram

| File                                       | Format   | Description                                              |
| ------------------------------------------ | -------- | -------------------------------------------------------- |
| [d1-class-model.puml](d1-class-model.puml) | PlantUML | Full 21-entity domain model with relationships and enums |

## Domain Overview

| File                                           | Format | Description                                                    |
| ---------------------------------------------- | ------ | -------------------------------------------------------------- |
| [d2-domain-overview.d2](d2-domain-overview.d2) | D2     | High-level architecture showing entity groups and interactions |

## State Diagrams

| File                                                             | Format  | Description                                                                   |
| ---------------------------------------------------------------- | ------- | ----------------------------------------------------------------------------- |
| [d3-expediente-states.mmd](d3-expediente-states.mmd)             | Mermaid | Expediente lifecycle: open, in_progress, pending_resolution, closed, archived |
| [d4-deadline-states.mmd](d4-deadline-states.mmd)                 | Mermaid | Deadline alerts: pending with escalation (7d, 3d, 1d), completed, overdue     |
| [d5-proceeding-states.mmd](d5-proceeding-states.mmd)             | Mermaid | Proceeding phases: filing through appeal and execution                        |
| [d6-appointment-states.mmd](d6-appointment-states.mmd)           | Mermaid | Appointment flow: scheduled, reschedule_requested, confirmed, completed       |
| [d7-invoice-states.mmd](d7-invoice-states.mmd)                   | Mermaid | Invoice lifecycle: draft (VeriFactu), unpaid, partial, paid, overdue          |
| [d8-document-request-states.mmd](d8-document-request-states.mmd) | Mermaid | Document request cycle: pending, submitted, accepted, rejected                |

## Flow Diagrams

| File                                                       | Format  | Description                                                     |
| ---------------------------------------------------------- | ------- | --------------------------------------------------------------- |
| [f1-expediente-lifecycle.mmd](f1-expediente-lifecycle.mmd) | Mermaid | End-to-end expediente lifecycle from intake to archive          |
| [f2-deadline-calculation.mmd](f2-deadline-calculation.mmd) | Mermaid | Dias habiles algorithm (skip weekends, holidays, August recess) |
| [f3-document-review.mmd](f3-document-review.mmd)           | Mermaid | Document collection and review loop with accept/reject          |

## Sequence Diagrams

| File                                                                     | Format   | Description                                                   |
| ------------------------------------------------------------------------ | -------- | ------------------------------------------------------------- |
| [seq-01-create-expediente.puml](seq-01-create-expediente.puml)           | PlantUML | Expediente creation with conflict-of-interest check           |
| [seq-02-deadline-alert.puml](seq-02-deadline-alert.puml)                 | PlantUML | CRON-based deadline escalation chain                          |
| [seq-03-document-collection.puml](seq-03-document-collection.puml)       | PlantUML | Document request, upload, review, and accept/reject cycle     |
| [seq-04-hearing-workflow.puml](seq-04-hearing-workflow.puml)             | PlantUML | LexNET notification to hearing outcome and appeal deadline    |
| [seq-05-invoice-verifactu.puml](seq-05-invoice-verifactu.puml)           | PlantUML | Invoice calculation (IVA/IRPF), VeriFactu QR, AEAT submission |
| [seq-06-client-portal-login.puml](seq-06-client-portal-login.puml)       | PlantUML | Dual auth paths (password and magic link) with JWT            |
| [seq-07-proceeding-lifecycle.puml](seq-07-proceeding-lifecycle.puml)     | PlantUML | Full proceeding lifecycle with appeal chain                   |
| [seq-08-appointment-reschedule.puml](seq-08-appointment-reschedule.puml) | PlantUML | Client reschedule request, negotiation, and confirmation      |

## Rendering

### PlantUML

```bash
# Single file
java -jar plantuml.jar -tsvg d1-class-model.puml

# All PlantUML files
java -jar plantuml.jar -tsvg "*.puml"
```

### Mermaid

```bash
# Single file (requires @mermaid-js/mermaid-cli)
npx mmdc -i d3-expediente-states.mmd -o d3-expediente-states.svg \
  -c mermaid-config.json

# All Mermaid files
for f in *.mmd; do
  npx mmdc -i "$f" -o "${f%.mmd}.svg" -c mermaid-config.json
done
```

### D2

```bash
d2 d2-domain-overview.d2 d2-domain-overview.svg
```
