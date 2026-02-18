# Data Model — Conceptual Design

Status: Draft — pending review. Level: Conceptual model (technology-agnostic). Principle: Contract
First — this model drives YAML schemas in `src/contracts/`. Scope: **Legal vertical only** (see
[ADR-0005](../adrs/ADR-0005-legal-vertical-scope.md)).

## Design Principles

1. **Technology-agnostic**: Entities, relationships, and rules. No DB engine assumed.
2. **Legal-vertical scope**: This model targets law firm (despacho) workflows per ADR-0005. Fiscal
   and labor entities are out of scope.
3. **Events as audit trail**: State changes produce immutable events for auditing.
4. **UUIDv7 for all entity IDs**: Time-sortable, globally unique, no auto-increment.
5. **Contract First**: This model drives YAML contracts, which generate TypeScript types and Python
   models.
6. **Expediente as central organizer**: Replaces the generic "Matter" entity. All work revolves
   around expedientes.
7. **Proceeding chain**: An expediente may contain multiple proceedings linked sequentially (first
   instance → appeal → cassation) via `previousProceedingId`.
8. **Domain-specific naming**: Entity names reflect the Spanish legal domain while keeping English
   identifiers in code.

## Entity Map

### Core Entities (19)

```text
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
```

### Auth Entities (2)

```text
User (1:1 with Member or Client)
Token (refresh tokens)
```

**Total: 21 stored entities** (up from 9).

## Entity Definitions

### Firm

The tenant. A law firm (despacho de abogados).

| Attribute | Type        | Required | Description             |
| --------- | ----------- | :------: | ----------------------- |
| id        | UUIDv7 (PK) |   Yes    |                         |
| name      | Text        |   Yes    | Firm display name       |
| taxId     | Text        |    No    | NIF/CIF                 |
| sector    | Enum        |   Yes    | `legal`, `tax`, `labor` |
| address   | Text        |    No    |                         |
| phone     | Text        |    No    |                         |
| email     | Text        |   Yes    | Primary contact email   |
| createdAt | DateTime    |   Yes    | Auto-set                |
| updatedAt | DateTime    |   Yes    | Auto-set                |

### Member

A user belonging to the firm.

| Attribute      | Type        | Required | Description                                                  |
| -------------- | ----------- | :------: | ------------------------------------------------------------ |
| id             | UUIDv7 (PK) |   Yes    |                                                              |
| firmId         | UUIDv7 (FK) |   Yes    | Belongs to Firm                                              |
| userId         | UUIDv7 (FK) |   Yes    | Links to User (auth)                                         |
| role           | Enum        |   Yes    | `managing_partner`, `associate`, `paralegal`, `office_admin` |
| firstName      | Text        |   Yes    |                                                              |
| lastName       | Text        |   Yes    |                                                              |
| email          | Text        |   Yes    |                                                              |
| phone          | Text        |    No    |                                                              |
| barNumber      | Text        |    No    | Numero de colegiado                                          |
| barAssociation | Text        |    No    | Colegio de abogados                                          |
| supervisorId   | UUIDv7 (FK) |    No    | Which associate(s) a paralegal supports                      |
| active         | Boolean     |   Yes    | Default: true                                                |
| createdAt      | DateTime    |   Yes    |                                                              |
| updatedAt      | DateTime    |   Yes    |                                                              |

### Client

External person or entity receiving legal services.

| Attribute | Type        | Required | Description                                  |
| --------- | ----------- | :------: | -------------------------------------------- |
| id        | UUIDv7 (PK) |   Yes    |                                              |
| firmId    | UUIDv7 (FK) |   Yes    | Belongs to Firm                              |
| firstName | Text        |   Yes    |                                              |
| lastName  | Text        |   Yes    |                                              |
| email     | Text        |   Yes    |                                              |
| phone     | Text        |    No    |                                              |
| taxId     | Text        |    No    | NIF/CIF                                      |
| legalName | Text        |    No    | Registered legal name (if company)           |
| address   | Text        |    No    |                                              |
| type      | Enum        |    No    | `individual`, `freelancer`, `sme`, `company` |
| active    | Boolean     |   Yes    | Default: true                                |
| createdAt | DateTime    |   Yes    |                                              |
| updatedAt | DateTime    |   Yes    |                                              |

### Expediente

Central work unit — the complete dossier for a client engagement. Replaces the generic "Matter"
entity. An expediente may contain multiple proceedings, parties, documents, appointments,
communications, time entries, and invoices.

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
| caseNumber   | Text        |    No    | Court case number (numero de autos)                               |
| amount       | Decimal     |    No    | Amount in dispute (cuantia)                                       |
| billingModel | Enum        |    No    | `hourly`, `fixed_fee`, `retainer`, `mixed`, `success_fee`         |
| parentId     | UUIDv7 (FK) |    No    | Parent expediente (for related cases)                             |
| createdAt    | DateTime    |   Yes    |                                                                   |
| updatedAt    | DateTime    |   Yes    |                                                                   |

**Status transitions**:

```text
open → in_progress → pending_resolution → closed → archived
```

### Court

Court of law (juzgado). Firm-scoped directory of courts used by the firm.

| Attribute        | Type        | Required | Description                                                    |
| ---------------- | ----------- | :------: | -------------------------------------------------------------- |
| id               | UUIDv7 (PK) |   Yes    |                                                                |
| firmId           | UUIDv7 (FK) |   Yes    | Belongs to Firm (firm-scoped directory)                        |
| name             | Text        |   Yes    | Full name (e.g., "Juzgado de Primera Instancia n.3 de Madrid") |
| type             | Text        |   Yes    | Court type (Primera Instancia, Instruccion, Mercantil, etc.)   |
| jurisdiction     | Enum        |   Yes    | `civil`, `criminal`, `labor`, `administrative`                 |
| judicialDistrict | Text        |   Yes    | Partido judicial                                               |
| province         | Text        |   Yes    | Province                                                       |
| address          | Text        |    No    | Physical address                                               |
| phone            | Text        |    No    | Contact phone                                                  |
| email            | Text        |    No    | Contact email                                                  |
| active           | Boolean     |   Yes    | Default: true                                                  |
| createdAt        | DateTime    |   Yes    |                                                                |
| updatedAt        | DateTime    |   Yes    |                                                                |

### CourtAgent

Court representative (procurador). Firm-scoped directory of procuradores the firm works with.

| Attribute         | Type        | Required | Description                            |
| ----------------- | ----------- | :------: | -------------------------------------- |
| id                | UUIDv7 (PK) |   Yes    |                                        |
| firmId            | UUIDv7 (FK) |   Yes    | Belongs to Firm                        |
| firstName         | Text        |   Yes    |                                        |
| lastName          | Text        |   Yes    |                                        |
| barNumber         | Text        |    No    | Numero de colegiado                    |
| barAssociation    | Text        |    No    | Colegio de Procuradores                |
| judicialDistricts | Text[]      |    No    | Partidos judiciales where they operate |
| phone             | Text        |    No    |                                        |
| email             | Text        |    No    |                                        |
| active            | Boolean     |   Yes    | Default: true                          |
| createdAt         | DateTime    |   Yes    |                                        |
| updatedAt         | DateTime    |   Yes    |                                        |

### Party

A person or entity with a procedural role in an expediente (parte procesal).

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

### Proceeding

A judicial proceeding (procedimiento) within an expediente. An expediente may contain multiple
proceedings (e.g., main case, interim measures, appeal). Proceedings can be linked sequentially via
`previousProceedingId` to form an appeal chain.

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

### ProceduralAction

An event in a proceeding (actuacion procesal) such as filing a demanda, receiving a sentencia,
submitting evidence, etc.

| Attribute        | Type        | Required | Description                                             |
| ---------------- | ----------- | :------: | ------------------------------------------------------- |
| id               | UUIDv7 (PK) |   Yes    |                                                         |
| proceedingId     | UUIDv7 (FK) |   Yes    | Belongs to Proceeding                                   |
| type             | Text        |   Yes    | demanda, contestacion, prueba, sentencia, recurso, etc. |
| date             | Date        |   Yes    | When the action occurred                                |
| notificationDate | Date        |    No    | When notified (for court actions)                       |
| description      | Text        |    No    | Description of the action                               |
| responsibleId    | UUIDv7 (FK) |    No    | Assigned member                                         |
| status           | Enum        |   Yes    | `pending`, `completed`, `overdue`                       |
| createdAt        | DateTime    |   Yes    |                                                         |

### Resolution

A court decision (resolucion judicial) issued in a proceeding.

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

### Hearing

A scheduled court hearing (vista / senalamiento) within a proceeding.

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

### Deadline

A procedural or internal deadline (plazo) linked to an expediente and optionally to a specific
proceeding. Deadline calculation must account for dias habiles (working days), weekends, holidays,
and the August judicial recess.

| Attribute          | Type        | Required | Description                                                     |
| ------------------ | ----------- | :------: | --------------------------------------------------------------- |
| id                 | UUIDv7 (PK) |   Yes    |                                                                 |
| expedienteId       | UUIDv7 (FK) |   Yes    | Belongs to Expediente                                           |
| proceedingId       | UUIDv7 (FK) |    No    | Optionally linked to a specific proceeding                      |
| type               | Enum        |   Yes    | `procedural`, `prescription`, `expiration`, `internal`          |
| description        | Text        |   Yes    | What must be done                                               |
| startDate          | Date        |   Yes    | When deadline starts counting                                   |
| dueDate            | Date        |   Yes    | Calculated due date                                             |
| workingDays        | Boolean     |   Yes    | Whether counted in dias habiles                                 |
| durationDays       | Integer     |   Yes    | Number of days (for calculation audit)                          |
| consequence        | Enum        |    No    | `preclusion`, `case_abandonment`, `prescription_expiry`, `none` |
| responsibleId      | UUIDv7 (FK) |   Yes    | Assigned member                                                 |
| status             | Enum        |   Yes    | `pending`, `completed`, `overdue`                               |
| completedAt        | DateTime    |    No    | When marked as done                                             |
| completionEvidence | Text        |    No    | Proof (e.g., LexNET receipt number)                             |
| createdAt          | DateTime    |   Yes    |                                                                 |
| updatedAt          | DateTime    |   Yes    |                                                                 |

### DocumentRequest

A request for documents from a client within an expediente.

| Attribute       | Type        | Required | Description                                    |
| --------------- | ----------- | :------: | ---------------------------------------------- |
| id              | UUIDv7 (PK) |   Yes    |                                                |
| expedienteId    | UUIDv7 (FK) |   Yes    | Belongs to Expediente                          |
| title           | Text        |   Yes    | "ID card", "Tax certificate", etc.             |
| description     | Text        |    No    | What the client should provide                 |
| dueDate         | Date        |    No    | When the document is needed by                 |
| status          | Enum        |   Yes    | `pending`, `submitted`, `accepted`, `rejected` |
| rejectionReason | Text        |    No    | Why the document was rejected                  |
| createdAt       | DateTime    |   Yes    |                                                |
| updatedAt       | DateTime    |   Yes    |                                                |

### Document

An uploaded file associated with a document request.

| Attribute   | Type        | Required | Description                |
| ----------- | ----------- | :------: | -------------------------- |
| id          | UUIDv7 (PK) |   Yes    |                            |
| requestId   | UUIDv7 (FK) |   Yes    | Belongs to DocumentRequest |
| fileName    | Text        |   Yes    | Original filename          |
| mimeType    | Text        |   Yes    | File MIME type             |
| sizeBytes   | Integer     |   Yes    | File size                  |
| storagePath | Text        |   Yes    | Internal storage location  |
| uploadedBy  | UUIDv7 (FK) |   Yes    | Client who uploaded        |
| uploadedAt  | DateTime    |   Yes    |                            |

### Appointment

A scheduled client meeting. Distinct from court hearings (Hearing entity).

| Attribute    | Type        | Required | Description                                                                |
| ------------ | ----------- | :------: | -------------------------------------------------------------------------- |
| id           | UUIDv7 (PK) |   Yes    |                                                                            |
| firmId       | UUIDv7 (FK) |   Yes    | Belongs to Firm                                                            |
| expedienteId | UUIDv7 (FK) |    No    | Optionally linked to an expediente                                         |
| clientId     | UUIDv7 (FK) |   Yes    | Which client                                                               |
| memberId     | UUIDv7 (FK) |   Yes    | Which associate/professional                                               |
| scheduledBy  | UUIDv7 (FK) |   Yes    | Who created (associate or paralegal)                                       |
| date         | Date        |   Yes    | Appointment date                                                           |
| startTime    | Time        |   Yes    | Start time                                                                 |
| duration     | Integer     |   Yes    | Duration in minutes                                                        |
| type         | Enum        |   Yes    | `consultation`, `follow_up`, `signing`                                     |
| location     | Enum        |   Yes    | `office`, `remote`, `client_site`                                          |
| notes        | Text        |    No    | Additional notes                                                           |
| status       | Enum        |   Yes    | `scheduled`, `confirmed`, `reschedule_requested`, `completed`, `cancelled` |
| createdAt    | DateTime    |   Yes    |                                                                            |
| updatedAt    | DateTime    |   Yes    |                                                                            |

### Message

A communication within the context of an expediente.

| Attribute    | Type        | Required | Description                |
| ------------ | ----------- | :------: | -------------------------- |
| id           | UUIDv7 (PK) |   Yes    |                            |
| expedienteId | UUIDv7 (FK) |   Yes    | Within which expediente    |
| senderId     | UUIDv7      |   Yes    | Member or Client who sent  |
| senderType   | Enum        |   Yes    | `member`, `client`         |
| body         | Text        |   Yes    | Message content            |
| readAt       | DateTime    |    No    | When the recipient read it |
| createdAt    | DateTime    |   Yes    |                            |

### Notification

System alert for firm members.

| Attribute     | Type        | Required | Description                                                                                                                                                                                 |
| ------------- | ----------- | :------: | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| id            | UUIDv7 (PK) |   Yes    |                                                                                                                                                                                             |
| memberId      | UUIDv7 (FK) |   Yes    | Recipient (Member)                                                                                                                                                                          |
| type          | Enum        |   Yes    | `document_submitted`, `document_rejected`, `deadline_reminder`, `hearing_reminder`, `appointment_reminder`, `reschedule_request`, `all_documents_complete`, `new_message`, `new_resolution` |
| title         | Text        |   Yes    | Short notification title                                                                                                                                                                    |
| body          | Text        |    No    | Notification body                                                                                                                                                                           |
| referenceId   | UUIDv7      |    No    | ID of related entity                                                                                                                                                                        |
| referenceType | Text        |    No    | Entity type (`expediente`, `appointment`, `document_request`, `hearing`, `deadline`)                                                                                                        |
| read          | Boolean     |   Yes    | Default: false                                                                                                                                                                              |
| createdAt     | DateTime    |   Yes    |                                                                                                                                                                                             |

### TimeEntry

A billable (or non-billable) time entry linked to an expediente.

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

### Invoice

A client invoice (factura). Supports VeriFactu-compliant invoicing including IVA and IRPF
calculations, provisiones de fondos, and suplidos.

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

### User

Authentication identity. Linked 1:1 to a Member or Client.

| Attribute    | Type        | Required | Description           |
| ------------ | ----------- | :------: | --------------------- |
| id           | UUIDv7 (PK) |   Yes    |                       |
| email        | Text        |   Yes    | Login email (unique)  |
| passwordHash | Text        |   Yes    | Hashed password       |
| role         | Enum        |   Yes    | `member`, `client`    |
| active       | Boolean     |   Yes    | Default: true         |
| lastLoginAt  | DateTime    |    No    | Last successful login |
| createdAt    | DateTime    |   Yes    |                       |
| updatedAt    | DateTime    |   Yes    |                       |

### Token

Refresh token for authentication sessions.

| Attribute | Type        | Required | Description               |
| --------- | ----------- | :------: | ------------------------- |
| id        | UUIDv7 (PK) |   Yes    |                           |
| userId    | UUIDv7 (FK) |   Yes    | Belongs to User           |
| token     | Text        |   Yes    | Refresh token value       |
| expiresAt | DateTime    |   Yes    | Token expiration          |
| revokedAt | DateTime    |    No    | When revoked (if revoked) |
| createdAt | DateTime    |   Yes    |                           |

## Relationships

```text
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
Appointment N──1 Client
Appointment N──1 Member
User 1──1 Member (or Client)
User 1──N Token
```

## Indexes (recommended)

| Entity          | Index                             | Purpose                    |
| --------------- | --------------------------------- | -------------------------- |
| Expediente      | (firmId, status)                  | Dashboard queries          |
| Expediente      | (assigneeId, status)              | Associate workload         |
| Expediente      | (clientId)                        | Client portal              |
| Expediente      | (jurisdiction)                    | Category filtering         |
| Proceeding      | (expedienteId)                    | Proceedings per expediente |
| Deadline        | (responsibleId, status, dueDate)  | Deadline dashboard         |
| Deadline        | (expedienteId, status)            | Expediente deadlines       |
| Hearing         | (proceedingId, dateTime)          | Hearing calendar           |
| DocumentRequest | (expedienteId, status)            | Document tracking          |
| Appointment     | (memberId, date)                  | Calendar queries           |
| Appointment     | (clientId, date)                  | Client appointment view    |
| Message         | (expedienteId, createdAt)         | Conversation history       |
| Notification    | (memberId, read, createdAt)       | Unread notifications       |
| TimeEntry       | (expedienteId, memberId)          | Billing queries            |
| Invoice         | (firmId, clientId, paymentStatus) | Receivables                |
| Party           | (expedienteId)                    | Party listing              |
