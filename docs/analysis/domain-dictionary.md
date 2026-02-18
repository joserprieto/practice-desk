# Domain Dictionary

Scope: **Legal vertical only** (see
[ADR-0005](../architecture/adrs/ADR-0005-legal-vertical-scope.md)). Fiscal and labor terms are out
of scope for this version.

Code identifiers: **English**. UI strings: **Spanish**. API routes: **English**.

## Core Entities

| English (Code)   | Spanish (UI)            | Description                                                       |
| ---------------- | ----------------------- | ----------------------------------------------------------------- |
| Firm             | Despacho                | The tenant. A law firm                                            |
| Member           | Miembro                 | User belonging to the firm (associate, partner, paralegal, admin) |
| Client           | Cliente                 | External person or entity receiving legal services                |
| Expediente       | Expediente              | Central work unit — case dossier (same term in both languages)    |
| Proceeding       | Procedimiento           | Judicial proceeding within an expediente                          |
| ProceduralAction | Actuacion Procesal      | Event in a proceeding (filing, hearing, ruling)                   |
| Deadline         | Plazo                   | Procedural, prescription, or internal deadline                    |
| Hearing          | Vista / Senalamiento    | Scheduled court hearing                                           |
| Court            | Juzgado                 | Court of law                                                      |
| CourtAgent       | Procurador              | Court representative (procurador de los tribunales)               |
| Party            | Parte Procesal          | Person/entity with a role in a proceeding                         |
| Resolution       | Resolucion Judicial     | Court decision (sentencia, auto, providencia)                     |
| DocumentRequest  | Solicitud de Documentos | Request for documents from a client                               |
| Document         | Documento               | Uploaded or generated file                                        |
| Appointment      | Cita                    | Scheduled meeting (NOT a court hearing)                           |
| Message          | Mensaje                 | Communication within an expediente                                |
| Notification     | Notificacion            | System alert for firm members                                     |
| TimeEntry        | Registro de Tiempo      | Billable time entry linked to an expediente                       |
| Invoice          | Factura                 | Client invoice (VeriFactu compliant)                              |
| User             | Usuario                 | Authentication identity (1:1 with Member or Client)               |
| Token            | Token                   | Authentication refresh token                                      |

## Enums

### Member Roles

| Value (Code)     | Spanish (UI)      | Description                                    |
| ---------------- | ----------------- | ---------------------------------------------- |
| managing_partner | Socio Director    | Firm owner/partner with full access            |
| associate        | Abogado Asociado  | Licensed attorney with caseload                |
| paralegal        | Auxiliar Juridico | Support role, document and deadline management |
| office_admin     | Administrador     | System configuration and operations            |

### Expediente Status

| Value              | Spanish              | Description                                |
| ------------------ | -------------------- | ------------------------------------------ |
| open               | Abierto              | Newly created, not yet active              |
| in_progress        | En Tramite           | Active work in progress                    |
| pending_resolution | Pendiente Resolucion | Awaiting court decision or client response |
| closed             | Cerrado              | Work completed                             |
| archived           | Archivado            | Permanently stored, no further action      |

### Jurisdiction

| Value          | Spanish                    | Description                                                 |
| -------------- | -------------------------- | ----------------------------------------------------------- |
| civil          | Civil                      | Private disputes (contracts, family, inheritance, property) |
| criminal       | Penal                      | Criminal offenses                                           |
| labor          | Laboral / Social           | Employment and social security                              |
| administrative | Contencioso-Administrativo | Disputes with public administration                         |
| commercial     | Mercantil                  | Commercial law (insolvency, IP, competition)                |

### Matter Type (Civil examples)

| Value                  | Spanish                 | Description                           |
| ---------------------- | ----------------------- | ------------------------------------- |
| ordinary               | Juicio Ordinario        | Standard civil proceeding (>6000 EUR) |
| verbal                 | Juicio Verbal           | Simplified proceeding (<=6000 EUR)    |
| injunction             | Proceso Monitorio       | Payment order proceeding              |
| execution              | Ejecucion               | Enforcement of judgments              |
| voluntary_jurisdiction | Jurisdiccion Voluntaria | Non-contentious proceedings           |
| interim_measures       | Medidas Cautelares      | Interim/precautionary measures        |

### Procedural Phase

| Value               | Spanish          | Description                 |
| ------------------- | ---------------- | --------------------------- |
| filing              | Interposicion    | Case filed with the court   |
| admission           | Admision         | Court admits the case       |
| discovery           | Instruccion      | Evidence gathering phase    |
| preliminary_hearing | Audiencia Previa | Preliminary hearing (civil) |
| trial               | Juicio Oral      | Trial / oral hearing        |
| sentencing          | Sentencia        | Judgment phase              |
| appeal              | Recurso          | Appeal filed                |
| execution           | Ejecucion        | Enforcement of judgment     |

### Resolution Type

| Value       | Spanish     | Description                                          |
| ----------- | ----------- | ---------------------------------------------------- |
| sentencia   | Sentencia   | Formal judgment by judge                             |
| auto        | Auto        | Judicial order (procedural decisions)                |
| providencia | Providencia | Minor procedural direction                           |
| decreto     | Decreto     | Decision by Letrado de la Administracion de Justicia |

### Party Role

| Value              | Spanish               | Description                       |
| ------------------ | --------------------- | --------------------------------- |
| plaintiff          | Demandante            | Party bringing the claim          |
| defendant          | Demandado             | Party defending against the claim |
| co_defendant       | Codemandado           | Additional defendant              |
| third_party        | Tercero Interviniente | Third party joining proceedings   |
| private_prosecutor | Acusacion Particular  | Private prosecutor (criminal)     |
| accused            | Acusado               | Accused party (criminal)          |

### Deadline Type

| Value        | Spanish      | Description                           |
| ------------ | ------------ | ------------------------------------- |
| procedural   | Procesal     | Court-imposed procedural deadline     |
| prescription | Prescripcion | Legal time limit for bringing a claim |
| expiration   | Caducidad    | Time limit for exercising a right     |
| internal     | Interno      | Firm-internal deadline                |

### Deadline Consequence

| Value               | Spanish                    | Description                                            |
| ------------------- | -------------------------- | ------------------------------------------------------ |
| preclusion          | Preclusion                 | Loss of the right to perform the procedural act        |
| case_abandonment    | Caducidad de la Instancia  | Case abandonment (2 years 1st instance, 1 year appeal) |
| prescription_expiry | Extincion por Prescripcion | Claim becomes time-barred                              |
| none                | Ninguna                    | No legal consequence (internal deadline)               |

### Hearing Type

| Value               | Spanish          | Description                 |
| ------------------- | ---------------- | --------------------------- |
| preliminary_hearing | Audiencia Previa | Preliminary hearing (civil) |
| main_hearing        | Vista Principal  | Main hearing                |
| oral_trial          | Juicio Oral      | Oral trial                  |
| appearance          | Comparecencia    | Court appearance            |
| ratification        | Ratificacion     | Ratification hearing        |

### Appointment Type

| Value        | Spanish     | Description         |
| ------------ | ----------- | ------------------- |
| consultation | Consulta    | Client consultation |
| follow_up    | Seguimiento | Follow-up meeting   |
| signing      | Firma       | Document signing    |

### Appointment Location

| Value       | Spanish               | Description              |
| ----------- | --------------------- | ------------------------ |
| office      | Oficina               | At the firm's office     |
| remote      | Remoto                | Video call / remote      |
| client_site | Domicilio del Cliente | At the client's location |

### Invoice Type

| Value           | Spanish             | Description                                         |
| --------------- | ------------------- | --------------------------------------------------- |
| fee_note        | Nota de Honorarios  | Professional fee statement                          |
| final_invoice   | Factura Definitiva  | Final invoice                                       |
| proforma        | Proforma / Minuta   | Draft invoice for client review                     |
| advance_payment | Provision de Fondos | Advance payment invoice (subject to IVA)            |
| expenses_note   | Nota de Suplidos    | Third-party expenses statement (NOT subject to IVA) |

### Billing Model

| Value       | Spanish        | Description                     |
| ----------- | -------------- | ------------------------------- |
| hourly      | Por Horas      | Billed by the hour              |
| fixed_fee   | Precio Cerrado | Fixed fee for the entire matter |
| retainer    | Iguala         | Monthly/periodic retainer       |
| mixed       | Mixto          | Combination of models           |
| success_fee | Cuota Litis    | Contingency/success fee         |

### Document Request Status

| Value     | Spanish   | Description           |
| --------- | --------- | --------------------- |
| pending   | Pendiente | Not yet submitted     |
| submitted | Entregado | Submitted by client   |
| accepted  | Aceptado  | Reviewed and accepted |
| rejected  | Rechazado | Reviewed and rejected |

### Notification Type

| Value                  | Spanish                | Description                      |
| ---------------------- | ---------------------- | -------------------------------- |
| document_submitted     | Documento Entregado    | Client submitted a document      |
| document_rejected      | Documento Rechazado    | Document was rejected            |
| deadline_reminder      | Recordatorio de Plazo  | Approaching deadline alert       |
| hearing_reminder       | Recordatorio de Vista  | Upcoming hearing alert           |
| appointment_reminder   | Recordatorio de Cita   | Upcoming appointment alert       |
| new_message            | Mensaje Nuevo          | New message from client          |
| resolution_received    | Resolucion Recibida    | New court resolution             |
| all_documents_complete | Documentacion Completa | All requested documents received |

## Spanish Legal Institutional Terms

| Term                | Description                                                                                                                 |
| ------------------- | --------------------------------------------------------------------------------------------------------------------------- |
| LexNET              | Mandatory electronic judicial communications platform for filing and receiving court notifications                          |
| CTEAJE              | Catalogo de Entidades de la Administracion Judicial Electronica — official court directory codes                            |
| VeriFactu           | Electronic invoicing certification system, mandatory for professionals by July 2026                                         |
| AEAT                | Agencia Estatal de Administracion Tributaria — Spanish Tax Agency                                                           |
| Hoja de Encargo     | Professional engagement letter (mandatory per Consejo General de la Abogacia)                                               |
| Provision de Fondos | Advance payment from client (subject to IVA, requires invoice)                                                              |
| Suplidos            | Third-party expenses paid on behalf of client (NOT subject to IVA): tasas judiciales, gastos notariales, gastos registrales |
| Tasacion de Costas  | Formal calculation of recoverable court costs after favorable judgment                                                      |
| Jura de Cuentas     | Expedited procedure for lawyers/procuradores to collect unpaid professional fees                                            |
| Turno de Oficio     | Legal aid roster managed by the Colegio de Abogados                                                                         |
| Numero de Colegiado | Bar registration number for licensed attorneys                                                                              |
| Colegio de Abogados | Bar association (e.g., ICAM for Madrid, ICAB for Barcelona)                                                                 |
| Partido Judicial    | Judicial district — territorial unit for court jurisdiction                                                                 |
| Dias Habiles        | Working/business days (excluding weekends, holidays, August judicial recess)                                                |
| Cuota Litis         | Success/contingency fee arrangement                                                                                         |
| NIF                 | Numero de Identificacion Fiscal — tax ID for individuals                                                                    |
| CIF                 | Codigo de Identificacion Fiscal — tax ID for companies                                                                      |
| AutoFirma           | Official electronic signature application for Spanish digital certificates                                                  |

## Naming Conventions

| Rule             | Convention                          | Example                                 |
| ---------------- | ----------------------------------- | --------------------------------------- |
| Code identifiers | English, camelCase or PascalCase    | `expedienteId`, `CourtAgent`            |
| UI strings       | Spanish                             | "Nuevo Expediente", "Plazos Pendientes" |
| YAML contracts   | English, camelCase keys             | `billingModel`, `judicialDistrict`      |
| API routes       | English, kebab-case                 | `/api/expedientes`, `/api/court-agents` |
| DB columns       | English, snake_case                 | `case_number`, `judicial_district`      |
| TypeScript       | camelCase vars, PascalCase types    | `const expediente: Expediente`          |
| Python           | snake_case vars, PascalCase classes | `expediente: Expediente`                |
| Entity IDs       | UUIDv7                              | `0192d4e0-7b1a-7f5e-...`                |
