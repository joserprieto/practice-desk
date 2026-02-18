# Screen Inventory

Screens for Practice Desk UMFP. Each screen maps to features and user stories, defines data
requirements, and includes an ASCII mockup showing the intended layout.

## Conventions

- **Screen ID**: `S-NNN`
- **Access**: Which persona(s) see this screen
- **Features**: Which features this screen implements
- **Route**: Planned URL pattern
- **Mockup**: ASCII wireframe showing layout and key elements

---

## Professional Screens

### S-001: Partner Dashboard

| Field        | Value            |
| ------------ | ---------------- |
| **Access**   | Managing Partner |
| **Features** | F39, F14         |
| **Route**    | `/dashboard`     |

**ASCII Mockup:**

```
+-------------------------------------------------------------------+
|  PRACTICE DESK -- Partner Dashboard                    [Bell] [User]|
+---------+---------------------------------------------------------+
| NAV     |                                                         |
|         |  +------------+  +------------+  +------------+  +----+ |
| Dashboard|  | Active     |  | Deadlines  |  | Overdue    |  |Pend| |
| Exped.  |  | Expedientes|  | This Week  |  | Documents  |  |Inv.| |
| Deadlines|  |     42     |  |      7     |  |      3     |  |  5 | |
| Hearings|  +------------+  +------------+  +------------+  +----+ |
| Clients |                                                         |
| Invoices|  Approaching Deadlines               [View All ->]      |
| Reports |  +-----------------------------------------------------+|
| Settings|  | ! EXP-2026/042 | Contest. demanda | 3d  | Garcia   ||
|         |  | ! EXP-2026/038 | Recurso apelac.  | 5d  | Lopez    ||
|         |  | o EXP-2026/041 | Plazo prueba     | 8d  | Garcia   ||
|         |  | o EXP-2026/035 | Escrito concl.   | 12d | Martinez ||
|         |  +-----------------------------------------------------+|
|         |                                                         |
|         |  Workload Distribution                                  |
|         |  +-----------------------------------------------------+|
|         |  | Garcia    [========----------]  12 exp.              ||
|         |  | Lopez     [===========-------]  15 exp.              ||
|         |  | Martinez  [=====-------------]   8 exp.              ||
|         |  | Fernandez [========-----------]  10 exp.              ||
|         |  +-----------------------------------------------------+|
|         |                                                         |
|         |  Recent Activity                                        |
|         |  +-----------------------------------------------------+|
|         |  | 10:32 Garcia uploaded sentencia to EXP-2026/042     ||
|         |  | 09:15 Lopez closed EXP-2026/033                     ||
|         |  | 08:45 New client registered: Empresa ABC S.L.       ||
|         |  | Yesterday Martinez filed recurso EXP-2026/038       ||
|         |  +-----------------------------------------------------+|
+---------+---------------------------------------------------------+
```

**Data requirements:**

- **Expediente** (aggregate): count by status (abierto, en tramite, pendiente, cerrado)
- **Plazo** (deadline): due date, days remaining, legal consequence, linked expediente, assignee
- **Expediente per Associate**: count grouped by associate, status breakdown
- **AuditLog**: recent entries (timestamp, user, action, entity reference)
- **Factura** (invoice): count where status = pendiente

---

### S-002: Associate Dashboard

| Field        | Value        |
| ------------ | ------------ |
| **Access**   | Associate    |
| **Features** | F40, F13     |
| **Route**    | `/dashboard` |

**ASCII Mockup:**

```
+-------------------------------------------------------------------+
|  PRACTICE DESK -- Associate Dashboard                  [Bell] [User]|
+---------+---------------------------------------------------------+
| NAV     |                                                         |
|         |  Good morning, Ana Garcia              18 Feb 2026      |
| Dashboard|                                                         |
| Exped.  |  +------------+  +------------+  +------------+  +----+ |
| Deadlines|  | My Active  |  | Deadlines  |  | Pending    |  |Unbi| |
| Hearings|  | Expedientes|  | Today      |  | Doc Reqs   |  |lled| |
| Time    |  |     12     |  |      3     |  |      5     |  |4.5h| |
| Docs    |  +------------+  +------------+  +------------+  +----+ |
|         |                                                         |
|         |  Today's Deadlines                                      |
|         |  +-----------------------------------------------------+|
|         |  | !! EXP-2026/042 | Contest. demanda | HOY  | Civil   ||
|         |  | !  EXP-2026/045 | Proposic. prueba | HOY  | Laboral ||
|         |  | !  EXP-2026/048 | Recurso repos.   | HOY  | Civil   ||
|         |  +-----------------------------------------------------+|
|         |                                                         |
|         |  Today's Appointments                                   |
|         |  +-----------------------------------------------------+|
|         |  | 10:00 Consultation  | Sr. Perez     | Office Room 2 ||
|         |  | 12:30 Follow-up     | Empresa XYZ   | Video Call    ||
|         |  | 16:00 Doc signing   | Sra. Moreno   | Office Room 1 ||
|         |  +-----------------------------------------------------+|
|         |                                                         |
|         |  Pending Document Requests                              |
|         |  +-----------------------------------------------------+|
|         |  | EXP-2026/042 | 3 of 5 docs pending | Due: 20 Feb  ||
|         |  | EXP-2026/045 | 1 of 3 docs pending | Due: 25 Feb  ||
|         |  +-----------------------------------------------------+|
+---------+---------------------------------------------------------+
```

**Data requirements:**

- **Expediente**: own active expedientes (status, client, next deadline, jurisdiction)
- **Plazo**: deadlines due today and this week (due date, expediente ref, description)
- **Cita** (appointment): today's appointments (time, type, client, location)
- **DocumentRequest**: pending requests per expediente (submitted count, total count, due date)
- **TimeEntry**: unbilled hours total for current period

---

### S-003: Expediente List

| Field        | Value                         |
| ------------ | ----------------------------- |
| **Access**   | Associate, Paralegal, Partner |
| **Features** | F07                           |
| **Route**    | `/expedientes`                |

**ASCII Mockup:**

```
+-------------------------------------------------------------------+
|  PRACTICE DESK -- Expedientes                          [Bell] [User]|
+---------+---------------------------------------------------------+
| NAV     |                                                         |
|         |  Expedientes                             [+ New Exped.] |
|         |                                                         |
|         |  Filters:                                               |
|         |  [Status: All  v] [Jurisd: All v] [Assignee: All    v]  |
|         |  [Area: All    v] [Client: _______________] [Search___] |
|         |                                                         |
|         |  +-----------------------------------------------------+|
|         |  | Ref.       | Title        | Client  | Status | Jur. ||
|         |  |            |              |         |        | Next ||
|         |  |            |              |         |        | Dead ||
|         |  |            |              |         |        | Assig||
|         |  |------------|--------------|---------|--------|------||
|         |  | EXP-2026/  | Reclamacion  | Perez,  | En     | Civ. ||
|         |  | 042        | cantidad     | Juan    | tramite| 21Feb||
|         |  |            |              |         |        | Garci||
|         |  |------------|--------------|---------|--------|------||
|         |  | EXP-2026/  | Despido      | Empres  | Abierto| Lab. ||
|         |  | 045        | improcedente | a XYZ   |        | 25Feb||
|         |  |            |              |         |        | Lopez||
|         |  |------------|--------------|---------|--------|------||
|         |  | EXP-2026/  | Divorcio     | Moreno, | Pend.  | Civ. ||
|         |  | 048        | contencioso  | Maria   | client | 01Mar||
|         |  |            |              |         |        | Garci||
|         |  +-----------------------------------------------------+|
|         |                                                         |
|         |  Showing 1-25 of 42           [< Prev] [1] [2] [Next >]|
+---------+---------------------------------------------------------+
```

**Data requirements:**

- **Expediente**: reference, title, status, jurisdiction, area of law, matter type
- **Client**: name (persona fisica or juridica)
- **Plazo**: next upcoming deadline per expediente
- **User** (associate): assigned lawyer name
- Filters: status enum, jurisdiction enum, area of law enum, assignee list

---

### S-004: Expediente Detail

| Field        | Value                         |
| ------------ | ----------------------------- |
| **Access**   | Associate, Paralegal, Partner |
| **Features** | F01-F06, F08, F10, F15-F18    |
| **Route**    | `/expedientes/:id`            |

**ASCII Mockup:**

```
+-------------------------------------------------------------------+
|  PRACTICE DESK -- EXP-2026/042                         [Bell] [User]|
+---------+---------------------------------------------------------+
| NAV     |                                                         |
|         |  EXP-2026/042: Reclamacion de cantidad                  |
|         |  Status: [En tramite v]           [Edit] [Archive]      |
|         |                                                         |
|         |  [Overview] [Proceedings] [Deadlines] [Documents]       |
|         |  [Messages] [Time & Billing]                            |
|         |                                                         |
|         |  +----------------------------------+ +----------------+|
|         |  | OVERVIEW TAB                     | | SIDEBAR        ||
|         |  |                                  | |                ||
|         |  | Reference: EXP-2026/042          | | Parties        ||
|         |  | Area: Civil                      | | +------------+ ||
|         |  | Type: Ordinario                  | | |Demandante:  | ||
|         |  | Cuantia: 45.000 EUR              | | |Perez, Juan  | ||
|         |  | Filed: 15 Jan 2026               | | |Demandado:   | ||
|         |  |                                  | | |Banco ABC    | ||
|         |  | Client                           | | +------------+ ||
|         |  | +------------------------------+ | |                ||
|         |  | | Juan Perez Lopez             | | | Court          ||
|         |  | | NIF: 12345678A               | | | +------------+ ||
|         |  | | Tel: 612 345 678             | | | |Juzgado 1a   | ||
|         |  | +------------------------------+ | | |Inst. num 3   | ||
|         |  |                                  | | |Madrid         | ||
|         |  | Assigned: Ana Garcia             | | |Autos: 123/26 | ||
|         |  | Procurador: Pedro Ruiz           | | +------------+ ||
|         |  |                                  | |                ||
|         |  | Description                      | | Procurador     ||
|         |  | +------------------------------+ | | +------------+ ||
|         |  | | Client claims unpaid invoices| | | |Pedro Ruiz   | ||
|         |  | | totaling 45.000 EUR from...  | | | |Col. 1234    | ||
|         |  | +------------------------------+ | | +------------+ ||
|         |  +----------------------------------+ +----------------+|
+---------+---------------------------------------------------------+
```

**Data requirements:**

- **Expediente**: all fields (reference, title, status, area, type, cuantia, description, dates)
- **Client**: full contact info (name, NIF/CIF, phone, email, address)
- **Party**: all parties with procedural role (demandante, demandado, codemandado, tercero)
- **Court** (Juzgado): name, type, jurisdiction, numero de autos, judge
- **Procurador**: name, colegio, numero colegiado, contact
- **Proceeding** (Procedimiento): list with phase, type, linked actuaciones
- **Plazo**: deadlines for this expediente
- **Document**: documents attached to this expediente
- **Message**: communication thread
- **TimeEntry**: time entries and billing info

---

### S-005: Proceeding Timeline

| Field        | Value                               |
| ------------ | ----------------------------------- |
| **Access**   | Associate                           |
| **Features** | F15, F16, F18                       |
| **Route**    | `/expedientes/:id/proceedings/:pid` |

**ASCII Mockup:**

```
+-------------------------------------------------------------------+
|  PRACTICE DESK -- Proceeding Timeline                  [Bell] [User]|
+---------+---------------------------------------------------------+
| NAV     |                                                         |
|         |  EXP-2026/042 > Procedimiento Ordinario 123/2026        |
|         |  Phase: [Audiencia Previa]                               |
|         |                                                         |
|         |  Phase Progress:                                        |
|         |  [Demanda]--[Contestacion]-->[Aud.Previa]--[Juicio]     |
|         |    done        done           CURRENT       pending     |
|         |                                                         |
|         |  +-----------------------------------------------------+|
|         |  | TIMELINE                              [+ Actuacion] ||
|         |  |                                                     ||
|         |  |  15 Jan 2026                                        ||
|         |  |  o-- Presentacion demanda                           ||
|         |  |      Type: Escrito    Status: Presentado            ||
|         |  |      Docs: [demanda.pdf] [poder.pdf]                ||
|         |  |                                                     ||
|         |  |  22 Jan 2026                                        ||
|         |  |  o-- Admision a tramite (Providencia)               ||
|         |  |      Type: Resolucion  Result: Admitida             ||
|         |  |      Recurrible: No                                 ||
|         |  |                                                     ||
|         |  |  10 Feb 2026                                        ||
|         |  |  o-- Contestacion a la demanda                      ||
|         |  |      Type: Escrito    Status: Presentado            ||
|         |  |      Docs: [contestacion.pdf]                       ||
|         |  |                                                     ||
|         |  |  25 Feb 2026 (upcoming)                             ||
|         |  |  o-- Audiencia Previa (VISTA)                       ||
|         |  |      Court: Juzgado 1a Inst. num 3                  ||
|         |  |      Room: Sala 2    Time: 10:00                    ||
|         |  |      Status: Scheduled                              ||
|         |  +-----------------------------------------------------+|
+---------+---------------------------------------------------------+
```

**Data requirements:**

- **Procedimiento** (Proceeding): type, phase, linked expediente, court reference
- **Actuacion** (Procedural action): date, type (escrito, resolucion, diligencia), description,
  status
- **Resolucion**: type (sentencia, auto, providencia), result, firme/recurrible, notification date
- **Vista** (Hearing): date, time, court, courtroom, type, status
- **Document**: linked documents per actuacion

---

### S-006: Deadline Calendar

| Field        | Value                         |
| ------------ | ----------------------------- |
| **Access**   | Associate, Paralegal, Partner |
| **Features** | F13, F14                      |
| **Route**    | `/deadlines`                  |

**ASCII Mockup:**

```
+-------------------------------------------------------------------+
|  PRACTICE DESK -- Deadline Calendar                    [Bell] [User]|
+---------+---------------------------------------------------------+
| NAV     |                                                         |
|         |  Deadlines          [< Feb 2026 >]  [Month][Week][List] |
|         |                                                         |
|         |  +---+---+---+---+---+---+---+  +------------------+   |
|         |  |Mon|Tue|Wed|Thu|Fri|Sat|Sun|  | Upcoming         |   |
|         |  |---|---|---|---|---|---|---|  | Deadlines        |   |
|         |  |   |   |   |   |   |   | 1 |  |                  |   |
|         |  |---|---|---|---|---|---|---|  | 18 Feb (TODAY)   |   |
|         |  | 2 | 3 | 4 | 5 | 6 |   | 8 |  | [RED] Contest.   |   |
|         |  |   |   |   |   |   |   |   |  | demanda          |   |
|         |  |---|---|---|---|---|---|---|  | EXP-2026/042     |   |
|         |  | 9 |10 |11 |12 |13 |   |15 |  | Garcia           |   |
|         |  |   |   |   |   |   |   |   |  |                  |   |
|         |  |---|---|---|---|---|---|---|  | 20 Feb           |   |
|         |  |16 |17 |18*|19 |20 |   |22 |  | [ORG] Proposic.  |   |
|         |  |   |   |RED|   |ORG|   |   |  | prueba           |   |
|         |  |---|---|---|---|---|---|---|  | EXP-2026/045     |   |
|         |  |23 |24 |25 |26 |27 |   |   |  | Lopez            |   |
|         |  |   |   |GRN|   |   |   |   |  |                  |   |
|         |  +---+---+---+---+---+---+---+  | 25 Feb           |   |
|         |                                  | [GRN] Escrito    |   |
|         |  Legend:                          | conclusiones     |   |
|         |  [RED] Overdue                    | EXP-2026/048     |   |
|         |  [ORG] Approaching (< 5 days)    | Martinez         |   |
|         |  [GRN] On track                  |                  |   |
|         |                                  | [+ New Deadline] |   |
|         |                                  +------------------+   |
+---------+---------------------------------------------------------+
```

**Data requirements:**

- **Plazo** (Deadline): due date, description, type (procesal/prescripcion/internal), status
  (pending/completed/overdue), legal consequence
- **Expediente**: reference number, title (linked from deadline)
- **User**: assignee name
- **JudicialCalendar**: holidays, August recess (for date calculations)

---

### S-007: Hearing Calendar

| Field        | Value                |
| ------------ | -------------------- |
| **Access**   | Associate, Paralegal |
| **Features** | F17, F32             |
| **Route**    | `/hearings`          |

**ASCII Mockup:**

```
+-------------------------------------------------------------------+
|  PRACTICE DESK -- Hearing Calendar                     [Bell] [User]|
+---------+---------------------------------------------------------+
| NAV     |                                                         |
|         |  Hearings                [< Week 8, Feb 2026 >]         |
|         |                         [Month] [Week] [Day] [List]     |
|         |                                                         |
|         |  +-----------------------------------------------------+|
|         |  |       | Mon 16 | Tue 17 | Wed 18 | Thu 19 | Fri 20 ||
|         |  |-------|--------|--------|--------|--------|--------||
|         |  | 09:00 |        |        |+------+|        |        ||
|         |  |       |        |        ||Juicio ||        |        ||
|         |  | 10:00 |+------+|        ||Oral   ||        |+------+||
|         |  |       ||Aud.  ||        ||042    ||        ||Compa- |||
|         |  | 11:00 ||Previa||        ||Juzg.3 ||        ||recenc |||
|         |  |       ||045   ||        ||Sala 1 ||        ||048    |||
|         |  | 12:00 ||Juzg.5||        |+------+|        ||Juzg.7 |||
|         |  |       ||Sala 2||        |        |        |+------+||
|         |  | 13:00 |+------+|        |        |        |        ||
|         |  |       |        |        |        |        |        ||
|         |  +-----------------------------------------------------+|
|         |                                                         |
|         |  Upcoming Hearings                                      |
|         |  +-----------------------------------------------------+|
|         |  | 18 Feb 09:30 | Juicio Oral    | EXP-042 | Juzg. 3 ||
|         |  |              | Sala 1, Madrid | Garcia  |          ||
|         |  | 20 Feb 10:00 | Comparecencia  | EXP-048 | Juzg. 7 ||
|         |  |              | Sala 3, Madrid | Garcia  |          ||
|         |  | 25 Feb 11:00 | Aud. Previa    | EXP-051 | Juzg. 2 ||
|         |  |              | Sala 1, Alcala | Lopez   |          ||
|         |  +-----------------------------------------------------+|
+---------+---------------------------------------------------------+
```

**Data requirements:**

- **Vista** (Hearing): date, time, type (audiencia previa, juicio oral, comparecencia), status
- **Court** (Juzgado): name, address, courtroom (sala)
- **Expediente**: reference, title
- **User**: assigned associate
- **Proceeding**: linked proceeding context

---

### S-008: Document Requests

| Field        | Value                        |
| ------------ | ---------------------------- |
| **Access**   | Associate, Paralegal         |
| **Features** | F21, F22, F23                |
| **Route**    | `/expedientes/:id/documents` |

**ASCII Mockup:**

```
+-------------------------------------------------------------------+
|  PRACTICE DESK -- Documents: EXP-2026/042              [Bell] [User]|
+---------+---------------------------------------------------------+
| NAV     |                                                         |
|         |  EXP-2026/042 > Documents       [+ New Request] [Export]|
|         |                                                         |
|         |  Progress: 3 of 5 received       [===------] 60%       |
|         |                                                         |
|         |  Document Requests                                      |
|         |  +-----------------------------------------------------+|
|         |  | [v] DNI del demandante                               ||
|         |  |     Status: [ACCEPTED]   Uploaded: 16 Feb 2026      ||
|         |  |     File: dni_perez.pdf (245 KB)   [View] [Download]||
|         |  |-----------------------------------------------------||
|         |  | [v] Poder general para pleitos                       ||
|         |  |     Status: [ACCEPTED]   Uploaded: 16 Feb 2026      ||
|         |  |     File: poder_notarial.pdf (1.2 MB) [View] [Down] ||
|         |  |-----------------------------------------------------||
|         |  | [v] Facturas impagadas                               ||
|         |  |     Status: [SUBMITTED]  Uploaded: 17 Feb 2026      ||
|         |  |     File: facturas.zip (3.4 MB) [View] [Accept][Rej]||
|         |  |-----------------------------------------------------||
|         |  | [ ] Contrato de prestacion de servicios              ||
|         |  |     Status: [PENDING]    Due: 20 Feb 2026           ||
|         |  |     Description: Original or certified copy         ||
|         |  |     +-------------------------------------------+   ||
|         |  |     | Drag & drop file here or [Browse Files]   |   ||
|         |  |     +-------------------------------------------+   ||
|         |  |-----------------------------------------------------||
|         |  | [x] Extracto bancario ultimos 6 meses               ||
|         |  |     Status: [REJECTED]   Reason: Illegible scan     ||
|         |  |     Please resubmit a clearer copy                  ||
|         |  |     +-------------------------------------------+   ||
|         |  |     | Drag & drop file here or [Browse Files]   |   ||
|         |  |     +-------------------------------------------+   ||
|         |  +-----------------------------------------------------+|
+---------+---------------------------------------------------------+
```

**Data requirements:**

- **DocumentRequest**: checklist items with name, description, due date, expediente reference
- **Document**: file name, size, type, upload date, status (pending/submitted/accepted/rejected)
- **Review**: accept/reject action, rejection reason, reviewer, review date
- **Expediente**: reference, title (for breadcrumb)

---

### S-009: Client List

| Field        | Value                       |
| ------------ | --------------------------- |
| **Access**   | Associate, Paralegal, Admin |
| **Features** | F42                         |
| **Route**    | `/clients`                  |

**ASCII Mockup:**

```
+-------------------------------------------------------------------+
|  PRACTICE DESK -- Clients                              [Bell] [User]|
+---------+---------------------------------------------------------+
| NAV     |                                                         |
|         |  Clients                        [+ New Client] [Import] |
|         |                                                         |
|         |  [Search: ___________________]                          |
|         |  [Type: All v] [Status: Active v]                       |
|         |                                                         |
|         |  +-----------------------------------------------------+|
|         |  | Name            | NIF/CIF    | Type     | Email     ||
|         |  |                 |            |          | Phone     ||
|         |  |                 |            |          | Active Exp||
|         |  |-----------------|------------|----------|-----------|
|         |  | Perez Lopez,    | 12345678A  | Persona  | jperez@   ||
|         |  | Juan            |            | Fisica   | email.com ||
|         |  |                 |            |          | 612345678 ||
|         |  |                 |            |          | 2 exp.    ||
|         |  |                 |            |          | [View][Ed]||
|         |  |-----------------|------------|----------|-----------|
|         |  | Empresa XYZ     | B12345678  | Persona  | info@     ||
|         |  | S.L.            |            | Juridica | xyz.com   ||
|         |  |                 |            |          | 913456789 ||
|         |  |                 |            |          | 3 exp.    ||
|         |  |                 |            |          | [View][Ed]||
|         |  |-----------------|------------|----------|-----------|
|         |  | Moreno Diaz,    | 87654321B  | Persona  | mmoreno@  ||
|         |  | Maria           |            | Fisica   | email.com ||
|         |  |                 |            |          | 698765432 ||
|         |  |                 |            |          | 1 exp.    ||
|         |  |                 |            |          | [View][Ed]||
|         |  +-----------------------------------------------------+|
|         |                                                         |
|         |  Showing 1-25 of 89           [< Prev] [1] [2] [Next >]|
+---------+---------------------------------------------------------+
```

**Data requirements:**

- **Client**: name, NIF/CIF, type (persona fisica/juridica), email, phone, address, status
  (active/inactive)
- **Expediente** (aggregate): count of active expedientes per client
- Import source: CSV/Excel file upload for bulk client import

---

### S-010: Client Detail

| Field        | Value                |
| ------------ | -------------------- |
| **Access**   | Associate, Paralegal |
| **Features** | F42, F25             |
| **Route**    | `/clients/:id`       |

**ASCII Mockup:**

```
+-------------------------------------------------------------------+
|  PRACTICE DESK -- Client: Juan Perez Lopez             [Bell] [User]|
+---------+---------------------------------------------------------+
| NAV     |                                                         |
|         |  Juan Perez Lopez                          [Edit] [More]|
|         |                                                         |
|         |  +----------------------------------+ +----------------+|
|         |  | Contact Information              | | Quick Stats    ||
|         |  | NIF: 12345678A                   | | Active Exp: 2  ||
|         |  | Email: jperez@email.com          | | Closed Exp: 1  ||
|         |  | Phone: 612 345 678               | | Pending Docs: 3||
|         |  | Address: C/ Mayor 15, Madrid     | | Open Invoices:1||
|         |  | Type: Persona Fisica             | +----------------+|
|         |  | Since: 10 Jan 2025               |                   |
|         |  +----------------------------------+                   |
|         |                                                         |
|         |  [Expedientes] [Communications] [Documents] [Invoices]  |
|         |                                                         |
|         |  Expedientes                                            |
|         |  +-----------------------------------------------------+|
|         |  | EXP-2026/042 | Reclamacion cantidad | En tramite    ||
|         |  |              | Garcia              | Next: 21 Feb  ||
|         |  |----------------------------------------------------||
|         |  | EXP-2026/055 | Arrendamiento       | Abierto       ||
|         |  |              | Lopez               | Next: 05 Mar  ||
|         |  |----------------------------------------------------||
|         |  | EXP-2025/088 | Herencia            | Cerrado       ||
|         |  |              | Garcia              | Closed: Dec 25||
|         |  +-----------------------------------------------------+|
|         |                                                         |
|         |  Recent Communications                                  |
|         |  +-----------------------------------------------------+|
|         |  | 17 Feb | Garcia: Documentos recibidos, revisando..  ||
|         |  | 15 Feb | Client: Adjunto las facturas solicitadas   ||
|         |  | 14 Feb | Garcia: Necesitamos los documentos antes...||
|         |  +-----------------------------------------------------+|
+---------+---------------------------------------------------------+
```

**Data requirements:**

- **Client**: all fields (name, NIF/CIF, type, email, phone, address, registration date)
- **Expediente**: list with reference, title, status, assignee, next deadline
- **Message**: recent communications across all expedientes for this client
- **Document**: shared documents across all expedientes
- **Factura**: invoices linked to this client

---

### S-011: Court Directory

| Field        | Value            |
| ------------ | ---------------- |
| **Access**   | Paralegal, Admin |
| **Features** | F20              |
| **Route**    | `/courts`        |

**ASCII Mockup:**

```
+-------------------------------------------------------------------+
|  PRACTICE DESK -- Court Directory                      [Bell] [User]|
+---------+---------------------------------------------------------+
| NAV     |                                                         |
|         |  Courts (Juzgados)                       [+ Add Court]  |
|         |                                                         |
|         |  [Search: ___________________]                          |
|         |  [Type: All v] [Jurisdiction: All v] [Province: All  v] |
|         |                                                         |
|         |  +-----------------------------------------------------+|
|         |  | Name              | Type      | Jurisdiction         ||
|         |  |                   | District  | Province   | Actions ||
|         |  |-------------------|-----------|----------------------||
|         |  | Juzgado de 1a     | 1a        | Civil                ||
|         |  | Instancia num 3   | Instancia | Partido Jud. Madrid  ||
|         |  |                   |           | Madrid     | [Ed][Vw]||
|         |  |-------------------|-----------|----------------------||
|         |  | Juzgado de lo     | Social    | Laboral              ||
|         |  | Social num 5      |           | Partido Jud. Madrid  ||
|         |  |                   |           | Madrid     | [Ed][Vw]||
|         |  |-------------------|-----------|----------------------||
|         |  | Juzgado de lo     | Contenc.  | Cont.-Administrativo ||
|         |  | Cont-Admin num 2  | Admin.    | Partido Jud. Alcala  ||
|         |  |                   |           | Madrid     | [Ed][Vw]||
|         |  |-------------------|-----------|----------------------||
|         |  | Juzgado de        | Instruc.  | Penal                ||
|         |  | Instruccion num 1 |           | Partido Jud. Getafe  ||
|         |  |                   |           | Madrid     | [Ed][Vw]||
|         |  +-----------------------------------------------------+|
|         |                                                         |
|         |  Showing 1-25 of 156          [< Prev] [1] [2] [Next >]|
+---------+---------------------------------------------------------+
```

**Data requirements:**

- **Juzgado** (Court): name, type, jurisdiction, partido judicial, province, address, phone, fax
- Filters: type enum, jurisdiction enum, province enum
- Search: by name or partido judicial

---

### S-012: Time Tracking

| Field        | Value     |
| ------------ | --------- |
| **Access**   | Associate |
| **Features** | F34       |
| **Route**    | `/time`   |

**ASCII Mockup:**

```
+-------------------------------------------------------------------+
|  PRACTICE DESK -- Time Tracking                        [Bell] [User]|
+---------+---------------------------------------------------------+
| NAV     |                                                         |
|         |  Time Tracking                                          |
|         |                                                         |
|         |  +-----------------------------------------------------+|
|         |  | TIMER                                                ||
|         |  | Expediente: [EXP-2026/042 - Reclamacion cant... v]   ||
|         |  | Description: [Revision documentacion aportada___]    ||
|         |  | Billable: [x]                                       ||
|         |  |                                                      ||
|         |  |              [ 01:23:45 ]                            ||
|         |  |         [Start]  [Pause]  [Stop & Save]             ||
|         |  +-----------------------------------------------------+|
|         |                                                         |
|         |  Weekly Summary (17-21 Feb 2026)                        |
|         |  +-----------------------------------------------------+|
|         |  | Billable:     18h 30m    |  Target: 32h             ||
|         |  | Non-billable:  4h 15m    |  [=========-------] 58%  ||
|         |  +-----------------------------------------------------+|
|         |                                                         |
|         |  Time Entries                      [+ Manual Entry]     |
|         |  +-----------------------------------------------------+|
|         |  | Date     | Expediente   | Duration | Desc.  |Billable|
|         |  |----------|--------------|----------|--------|--------|
|         |  | 18 Feb   | EXP-2026/042 | 1h 30m   | Revis. | [x]   |
|         |  | 18 Feb   | EXP-2026/045 | 0h 45m   | Call   | [x]   |
|         |  | 17 Feb   | EXP-2026/042 | 2h 00m   | Draft  | [x]   |
|         |  | 17 Feb   | --Internal-- | 0h 30m   | Meeting| [ ]   |
|         |  | 17 Feb   | EXP-2026/048 | 1h 15m   | Invest.| [x]   |
|         |  | 16 Feb   | EXP-2026/042 | 3h 00m   | Escrito| [x]   |
|         |  +-----------------------------------------------------+|
|         |                                                         |
|         |  Showing entries for [This Week v]  [Export CSV]        |
+---------+---------------------------------------------------------+
```

**Data requirements:**

- **TimeEntry**: date, duration, description, billable flag, expediente reference, user
- **Expediente**: reference and title (for selector)
- **WeeklySummary** (aggregate): total billable, total non-billable, target hours
- Timer state: running/paused/stopped, elapsed time, linked expediente

---

### S-013: Invoicing

| Field        | Value              |
| ------------ | ------------------ |
| **Access**   | Paralegal, Partner |
| **Features** | F35, F36, F37      |
| **Route**    | `/invoices`        |

**ASCII Mockup:**

```
+-------------------------------------------------------------------+
|  PRACTICE DESK -- Invoices                             [Bell] [User]|
+---------+---------------------------------------------------------+
| NAV     |                                                         |
|         |  Invoices                               [+ New Invoice] |
|         |                                                         |
|         |  [Status: All v] [Client: ________] [Date: Feb 2026 v] |
|         |                                                         |
|         |  +-----------------------------------------------------+|
|         |  | Number     | Client    | Expediente | Amount  |Status|
|         |  |            |           |            |         | Date |
|         |  |------------|-----------|------------|---------|------|
|         |  | F-2026/015 | Perez, J. | EXP-042    | 2.450,00| PAID |
|         |  |            |           |            |  EUR    |15 Feb|
|         |  |------------|-----------|------------|---------|------|
|         |  | F-2026/016 | Empresa   | EXP-045    | 5.800,00| SENT |
|         |  |            | XYZ S.L.  |            |  EUR    |17 Feb|
|         |  |------------|-----------|------------|---------|------|
|         |  | F-2026/017 | Moreno, M.| EXP-048    | 1.200,00| DRAFT|
|         |  |            |           |            |  EUR    |18 Feb|
|         |  +-----------------------------------------------------+|
|         |                                                         |
|         |  Invoice Detail: F-2026/016                             |
|         |  +-----------------------------------------------------+|
|         |  | Client: Empresa XYZ S.L.  | CIF: B12345678          ||
|         |  | Expediente: EXP-2026/045  | Despido improcedente    ||
|         |  |                                                     ||
|         |  | HONORARIOS                                          ||
|         |  | 12h @ 150 EUR/h  Asistencia letrada     1.800,00   ||
|         |  | Fixed fee         Estudio expediente       500,00   ||
|         |  |                                Subtotal  2.300,00   ||
|         |  |                                                     ||
|         |  | SUPLIDOS (exempt IVA)                               ||
|         |  | Tasas judiciales                           230,00   ||
|         |  | Procurador fees                            120,00   ||
|         |  |                                Subtotal    350,00   ||
|         |  |                                                     ||
|         |  | PROVISION DE FONDOS                                 ||
|         |  | Advance received 01 Feb                -1.000,00   ||
|         |  |                                                     ||
|         |  | Base imponible (honorarios)             2.300,00   ||
|         |  | IVA 21%                                   483,00   ||
|         |  | IRPF retention -15%                      -345,00   ||
|         |  | Suplidos                                  350,00   ||
|         |  | Provision applied                      -1.000,00   ||
|         |  | -------------------------------------------------  ||
|         |  | TOTAL                                   1.788,00   ||
|         |  |                                                     ||
|         |  | [Send to Client] [Download PDF] [Mark as Paid]      ||
|         |  +-----------------------------------------------------+|
+---------+---------------------------------------------------------+
```

**Data requirements:**

- **Factura** (Invoice): number, date, status (draft/sent/paid/partial/overdue), client, expediente
- **InvoiceLine**: concept, quantity, unit price, subtotal, type (honorario/suplido)
- **ProvisionDeFondos**: advance payments linked to expediente
- **TaxCalc**: IVA rate (21%), IRPF retention (15% standard, 7% first 3 years)
- **Payment**: date, amount, method, linked invoice
- **TimeEntry**: billable entries for invoice generation

---

### S-014: Reports

| Field        | Value          |
| ------------ | -------------- |
| **Access**   | Partner, Admin |
| **Features** | F41            |
| **Route**    | `/reports`     |

**ASCII Mockup:**

```
+-------------------------------------------------------------------+
|  PRACTICE DESK -- Reports                              [Bell] [User]|
+---------+---------------------------------------------------------+
| NAV     |                                                         |
|         |  Reports                                                |
|         |                                                         |
|         |  Report Type:                                           |
|         |  +-----------------------------------------------------+|
|         |  | [o] Expediente Activity                              ||
|         |  | [ ] Deadline Compliance                              ||
|         |  | [ ] Billing & Revenue                                ||
|         |  | [ ] Time Utilization                                 ||
|         |  | [ ] Document Collection Status                       ||
|         |  | [ ] Client Portfolio                                 ||
|         |  | [ ] Workload Distribution                            ||
|         |  +-----------------------------------------------------+|
|         |                                                         |
|         |  Filters:                                               |
|         |  Date Range: [01 Jan 2026] to [18 Feb 2026]            |
|         |  Associate:  [All               v]                     |
|         |  Area of Law:[All               v]                     |
|         |  Status:     [All               v]                     |
|         |                                                         |
|         |  [Generate Report]                                     |
|         |                                                         |
|         |  Preview: Expediente Activity Report                    |
|         |  +-----------------------------------------------------+|
|         |  | Period: 01 Jan - 18 Feb 2026                        ||
|         |  |                                                     ||
|         |  | New expedientes:    15    | Closed:       8         ||
|         |  | Active:             42    | Avg duration: 45 days   ||
|         |  |                                                     ||
|         |  | By Area:                                            ||
|         |  | Civil       [==========]  18                        ||
|         |  | Laboral     [=======]     12                        ||
|         |  | Penal       [====]         7                        ||
|         |  | Mercantil   [===]          5                        ||
|         |  |                                                     ||
|         |  | By Associate:                                       ||
|         |  | Garcia      12 | Lopez    10 | Martinez   8        ||
|         |  | Fernandez   7  | Sanchez   5 |                      ||
|         |  +-----------------------------------------------------+|
|         |                                                         |
|         |  [Export CSV] [Export PDF] [Print]                      |
+---------+---------------------------------------------------------+
```

**Data requirements:**

- **Expediente** (aggregate): counts by status, area, associate, date range
- **Plazo** (aggregate): compliance metrics (met on time, missed, average margin)
- **Factura** (aggregate): revenue totals, outstanding amounts, aging
- **TimeEntry** (aggregate): billable vs non-billable, utilization per associate
- **DocumentRequest** (aggregate): completion rates, average time to collect
- **Report config**: date range, filters, export format

---

### S-015: Firm Settings

| Field        | Value          |
| ------------ | -------------- |
| **Access**   | Admin, Partner |
| **Features** | F42, F43       |
| **Route**    | `/settings`    |

**ASCII Mockup:**

```
+-------------------------------------------------------------------+
|  PRACTICE DESK -- Settings                             [Bell] [User]|
+---------+---------------------------------------------------------+
| NAV     |                                                         |
|         |  Firm Settings                                          |
|         |                                                         |
|         |  [Firm Info] [Users & Roles] [Areas of Practice]        |
|         |  [Notifications] [Judicial Calendar] [RGPD]             |
|         |                                                         |
|         |  -- Firm Info Tab --                                    |
|         |  +-----------------------------------------------------+|
|         |  | Firm Name:    [Bufete Garcia & Asociados_________]   ||
|         |  | CIF:          [B98765432______]                      ||
|         |  | Address:      [C/ Serrano 45, 28001 Madrid______]   ||
|         |  | Phone:        [914 567 890____]                      ||
|         |  | Email:        [info@garciayasociados.es_________]   ||
|         |  | Website:      [www.garciayasociados.es__________]   ||
|         |  |                                                     ||
|         |  | Invoice Series: [F-2026/____]                        ||
|         |  | VeriFactu:      [x] Enabled                         ||
|         |  |                                                     ||
|         |  |                              [Save Changes]         ||
|         |  +-----------------------------------------------------+|
|         |                                                         |
|         |  -- Users & Roles Tab (preview) --                     |
|         |  +-----------------------------------------------------+|
|         |  | Name         | Role      | Email        | Status    ||
|         |  |--------------|-----------|--------------|-----------|
|         |  | A. Garcia    | Socio Dir.| ag@firma.es  | Active    ||
|         |  | R. Lopez     | Asociado  | rl@firma.es  | Active    ||
|         |  | L. Martinez  | Asociado  | lm@firma.es  | Active    ||
|         |  | S. Ruiz      | Paralegal | sr@firma.es  | Active    ||
|         |  | M. Torres    | Admin     | mt@firma.es  | Active    ||
|         |  |                                                     ||
|         |  |                         [+ Add User] [Manage Roles] ||
|         |  +-----------------------------------------------------+|
+---------+---------------------------------------------------------+
```

**Data requirements:**

- **Firm**: name, CIF, address, phone, email, website, invoice series, VeriFactu config
- **User**: name, email, role, status (active/inactive), permissions
- **Role**: name, permissions set
- **AreaOfPractice**: list of configured areas (civil, penal, laboral, mercantil, etc.)
- **NotificationConfig**: email templates, notification preferences
- **JudicialCalendar**: national holidays, autonomous community holidays, August recess
- **RGPDConfig**: retention periods, consent settings, data processing register

---

## Client Portal Screens

### S-016: Client Login

| Field        | Value           |
| ------------ | --------------- |
| **Access**   | Client          |
| **Features** | F28             |
| **Route**    | `/portal/login` |

**ASCII Mockup:**

```
+-------------------------------------------------------------------+
|                                                                   |
|                                                                   |
|              +-----------------------------------+                |
|              |                                   |                |
|              |    BUFETE GARCIA & ASOCIADOS       |                |
|              |    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~    |                |
|              |                                   |                |
|              |    Client Portal                  |                |
|              |                                   |                |
|              |    Email:                         |                |
|              |    [_____________________________] |                |
|              |                                   |                |
|              |    Password:                      |                |
|              |    [_____________________________] |                |
|              |                                   |                |
|              |    [x] Remember me                |                |
|              |                                   |                |
|              |    [        Log In          ]      |                |
|              |                                   |                |
|              |    -------- or --------           |                |
|              |                                   |                |
|              |    [   Send Magic Link     ]      |                |
|              |                                   |                |
|              |    Forgot password?                |                |
|              |                                   |                |
|              +-----------------------------------+                |
|                                                                   |
|              Powered by Practice Desk                             |
|                                                                   |
+-------------------------------------------------------------------+
```

**Data requirements:**

- **Authentication**: email, password hash, magic link token, session management
- **Firm**: name and branding (logo, colors) for portal customization
- **Client**: email for login lookup

---

### S-017: Client Dashboard

| Field        | Value     |
| ------------ | --------- |
| **Access**   | Client    |
| **Features** | F28       |
| **Route**    | `/portal` |

**ASCII Mockup:**

```
+-------------------------------------------------------------------+
|  BUFETE GARCIA & ASOCIADOS -- Client Portal        [Msgs(2)] [User]|
+-------------------------------------------------------------------+
|                                                                   |
|  Welcome, Juan Perez                                              |
|                                                                   |
|  Your Expedientes                                                 |
|  +------------------------------+ +-----------------------------+ |
|  | EXP-2026/042                 | | EXP-2026/055                | |
|  | Reclamacion de cantidad      | | Contrato arrendamiento      | |
|  |                              | |                             | |
|  | Status: En tramite           | | Status: Abierto             | |
|  | Phase:  Audiencia Previa     | | Phase:  Estudio inicial     | |
|  |                              | |                             | |
|  | Next: Vista 25 Feb 10:00    | | Next: Reunion 01 Mar       | |
|  |                              | |                             | |
|  | Pending docs: 2              | | Pending docs: 0             | |
|  | Unread msgs:  1              | | Unread msgs:  1             | |
|  |                              | |                             | |
|  | [View Details]               | | [View Details]              | |
|  +------------------------------+ +-----------------------------+ |
|                                                                   |
|  +------------------------------+ +-----------------------------+ |
|  | Upcoming                     | | Pending Actions             | |
|  |                              | |                             | |
|  | 25 Feb 10:00                | | 2 documents to submit       | |
|  | Vista - Juzgado 1a Inst. 3  | | for EXP-2026/042            | |
|  | Sala 1, Madrid              | | Due: 20 Feb 2026            | |
|  |                              | |                             | |
|  | 01 Mar 12:00                | | 1 invoice pending           | |
|  | Reunion - Office             | | F-2026/016: 5.800,00 EUR   | |
|  +------------------------------+ +-----------------------------+ |
|                                                                   |
+-------------------------------------------------------------------+
```

**Data requirements:**

- **Expediente**: client's active expedientes (reference, title, status, current phase)
- **Vista**: next upcoming hearing (date, time, court, sala)
- **DocumentRequest**: count of pending documents per expediente, due date
- **Message**: unread message count per expediente
- **Factura**: pending invoices (number, amount)
- **Cita**: upcoming appointments

---

### S-018: Client Expediente Detail

| Field        | Value                     |
| ------------ | ------------------------- |
| **Access**   | Client                    |
| **Features** | F29, F30                  |
| **Route**    | `/portal/expedientes/:id` |

**ASCII Mockup:**

```
+-------------------------------------------------------------------+
|  BUFETE GARCIA & ASOCIADOS -- Client Portal        [Msgs(2)] [User]|
+-------------------------------------------------------------------+
|                                                                   |
|  EXP-2026/042: Reclamacion de cantidad                            |
|  Status: En tramite                                               |
|  Your lawyer: Ana Garcia                                          |
|                                                                   |
|  Case Progress                                                    |
|  +---------------------------------------------------------------+|
|  | [Apertura]--[Demanda]--[Contestacion]-->[Aud.Previa]--[Juicio]||
|  |   done        done        done           NEXT          pending||
|  +---------------------------------------------------------------+|
|                                                                   |
|  [Documents] [Hearings] [Invoices] [Messages]                     |
|                                                                   |
|  Shared Documents                                                 |
|  +---------------------------------------------------------------+|
|  | Name                       | Date     | Action                ||
|  |----------------------------|----------|------------------------||
|  | Demanda presentada.pdf     | 15 Jan   | [Download] [View]    ||
|  | Admision a tramite.pdf     | 22 Jan   | [Download] [View]    ||
|  | Contestacion demanda.pdf   | 10 Feb   | [Download] [View]    ||
|  +---------------------------------------------------------------+|
|                                                                   |
|  Upcoming Hearings                                                |
|  +---------------------------------------------------------------+|
|  | 25 Feb 10:00 | Audiencia Previa | Juzgado 1a Inst. num 3     ||
|  |              | Sala 1, Madrid   | Bring: DNI, original docs  ||
|  +---------------------------------------------------------------+|
|                                                                   |
|  Invoices                                                         |
|  +---------------------------------------------------------------+|
|  | F-2026/015 | 15 Feb | 2.450,00 EUR | PAID                    ||
|  | F-2026/016 | 17 Feb | 5.800,00 EUR | PENDING  [Pay] [View]   ||
|  +---------------------------------------------------------------+|
|                                                                   |
|  Messages                            [Send Message]              |
|  +---------------------------------------------------------------+|
|  | 17 Feb 14:30 - Ana Garcia:                                    ||
|  | Hemos recibido la contestacion. La audiencia previa esta       ||
|  | fijada para el 25 de febrero. Por favor traiga los originales.||
|  |                                                                ||
|  | 17 Feb 15:10 - You:                                           ||
|  | Perfecto, alli estare. Necesito llevar algo mas?              ||
|  +---------------------------------------------------------------+|
+-------------------------------------------------------------------+
```

**Data requirements:**

- **Expediente**: reference, title, status, current phase, assigned lawyer
- **PhaseTimeline**: ordered phases with completion status (done/current/pending)
- **Document** (shared): documents the lawyer has shared with the client
- **Vista**: upcoming hearings (date, time, court, courtroom, preparation notes)
- **Factura**: invoices for this expediente (number, date, amount, status)
- **Message**: conversation thread (sender, timestamp, body)

---

### S-019: Client Document Upload

| Field        | Value                               |
| ------------ | ----------------------------------- |
| **Access**   | Client                              |
| **Features** | F22, F29                            |
| **Route**    | `/portal/expedientes/:id/documents` |

**ASCII Mockup:**

```
+-------------------------------------------------------------------+
|  BUFETE GARCIA & ASOCIADOS -- Client Portal        [Msgs(2)] [User]|
+-------------------------------------------------------------------+
|                                                                   |
|  EXP-2026/042 > Documents to Submit                              |
|                                                                   |
|  Your lawyer has requested the following documents.               |
|  Due date: 20 Feb 2026 (2 days remaining)                        |
|                                                                   |
|  Progress: 3 of 5 submitted                                      |
|  [==============================----------] 60%                   |
|                                                                   |
|  Document Checklist                                               |
|  +---------------------------------------------------------------+|
|  | [ACCEPTED]  DNI del demandante                                ||
|  |             Copia del documento nacional de identidad         ||
|  |             Submitted: 16 Feb   File: dni_perez.pdf           ||
|  |---------------------------------------------------------------|
|  | [ACCEPTED]  Poder general para pleitos                        ||
|  |             Poder notarial otorgado a favor del abogado       ||
|  |             Submitted: 16 Feb   File: poder_notarial.pdf      ||
|  |---------------------------------------------------------------|
|  | [SUBMITTED] Facturas impagadas                                ||
|  |             Todas las facturas pendientes de cobro            ||
|  |             Submitted: 17 Feb   File: facturas.zip            ||
|  |             Under review by your lawyer                       ||
|  |---------------------------------------------------------------|
|  | [PENDING]   Contrato de prestacion de servicios               ||
|  |             Contrato original firmado entre las partes        ||
|  |                                                               ||
|  |   +-------------------------------------------------------+  ||
|  |   |                                                       |  ||
|  |   |   Drag & drop your file here                         |  ||
|  |   |                                                       |  ||
|  |   |   or [Browse Files]   [Take Photo]                   |  ||
|  |   |                                                       |  ||
|  |   |   Accepted: PDF, JPG, PNG (max 10 MB)                |  ||
|  |   +-------------------------------------------------------+  ||
|  |---------------------------------------------------------------|
|  | [REJECTED]  Extracto bancario ultimos 6 meses                 ||
|  |             Extractos bancarios de los ultimos 6 meses        ||
|  |             Reason: Illegible scan - please resubmit clearer  ||
|  |                                                               ||
|  |   +-------------------------------------------------------+  ||
|  |   |                                                       |  ||
|  |   |   Drag & drop your file here                         |  ||
|  |   |                                                       |  ||
|  |   |   or [Browse Files]   [Take Photo]                   |  ||
|  |   +-------------------------------------------------------+  ||
|  +---------------------------------------------------------------+|
|                                                                   |
+-------------------------------------------------------------------+
```

**Data requirements:**

- **DocumentRequest**: checklist items (name, description, due date, expediente reference)
- **Document**: file name, size, type, upload date, status (pending/submitted/accepted/rejected)
- **Review**: rejection reason (if applicable)
- **Upload**: file input (drag-and-drop, file picker, camera capture for mobile)

---

### S-020: Client Messages

| Field        | Value              |
| ------------ | ------------------ |
| **Access**   | Client             |
| **Features** | F25                |
| **Route**    | `/portal/messages` |

**ASCII Mockup:**

```
+-------------------------------------------------------------------+
|  BUFETE GARCIA & ASOCIADOS -- Client Portal        [Msgs(2)] [User]|
+-------------------------------------------------------------------+
|                                                                   |
|  Messages                                                         |
|                                                                   |
|  +--------------------+  +--------------------------------------+ |
|  | Conversations      |  | EXP-2026/042 - Reclamacion cantidad | |
|  |                    |  |                                      | |
|  | +----------------+ |  | 14 Feb 10:00 - Ana Garcia:           | |
|  | |EXP-2026/042   *| |  | Buenos dias. Necesitamos que nos     | |
|  | |Reclamacion     | |  | remita los siguientes documentos     | |
|  | |cantidad        | |  | antes del 20 de febrero...           | |
|  | |Last: 17 Feb    | |  |                                      | |
|  | |Unread: 1       | |  | 15 Feb 09:30 - You:                  | |
|  | +----------------+ |  | Buenos dias. Adjunto el DNI y el     | |
|  |                    |  | poder notarial. Las facturas las     | |
|  | +----------------+ |  | envio manana.                        | |
|  | |EXP-2026/055    | |  |                                      | |
|  | |Contrato arren- | |  | 15 Feb 09:35 - Ana Garcia:           | |
|  | |damiento        | |  | Recibidos, gracias. Quedamos a la    | |
|  | |Last: 16 Feb    | |  | espera de las facturas.              | |
|  | |Unread: 1       | |  |                                      | |
|  | +----------------+ |  | 17 Feb 14:30 - Ana Garcia:           | |
|  |                    |  | Hemos recibido la contestacion a la  | |
|  |                    |  | demanda. La audiencia previa esta    | |
|  |                    |  | fijada para el 25 de febrero.        | |
|  |                    |  | Traiga los documentos originales.    | |
|  |                    |  |                                      | |
|  |                    |  | 17 Feb 15:10 - You:                  | |
|  |                    |  | Perfecto, alli estare. Necesito      | |
|  |                    |  | llevar algo mas?                     | |
|  |                    |  |                                      | |
|  |                    |  +--------------------------------------+ |
|  |                    |  | Type your message...                 | |
|  |                    |  |                          [Send]      | |
|  +--------------------+  +--------------------------------------+ |
|                                                                   |
+-------------------------------------------------------------------+
```

**Data requirements:**

- **Message**: sender, timestamp, body, read status, linked expediente
- **Expediente**: reference and title (for conversation grouping)
- **Conversation** (aggregate): last message date, unread count per expediente
- **Client**: name (for display)
- **User** (lawyer): name (for display)

---

## Summary

| Category      | Screens | IDs            |
| ------------- | ------- | -------------- |
| Professional  | 15      | S-001 to S-015 |
| Client Portal | 5       | S-016 to S-020 |
| **Total**     | **20**  |                |

---

## Traceability

| Screen | Features                   | Key Stories                                                   |
| ------ | -------------------------- | ------------------------------------------------------------- |
| S-001  | F39, F14                   | US-9.01, US-9.03, US-2.06                                     |
| S-002  | F40, F13                   | US-9.02, US-2.04, US-7.03                                     |
| S-003  | F07                        | US-1.05, US-1.07, US-1.10, US-1.11                            |
| S-004  | F01-F06, F08, F10, F15-F18 | US-1.01, US-1.02, US-1.03, US-1.04, US-1.06, US-3.01, US-3.02 |
| S-005  | F15, F16, F18              | US-3.01, US-3.02, US-3.05, US-3.06, US-3.08                   |
| S-006  | F13, F14                   | US-2.01, US-2.03, US-2.04, US-2.05, US-2.06                   |
| S-007  | F17, F32                   | US-3.03, US-3.04, US-3.07, US-7.03                            |
| S-008  | F21, F22, F23              | US-4.01, US-4.04, US-4.05, US-4.07                            |
| S-009  | F42                        | US-10.03, US-10.09                                            |
| S-010  | F42, F25                   | US-10.03, US-5.01, US-5.04                                    |
| S-011  | F20                        | US-3.13, US-10.06                                             |
| S-012  | F34                        | US-8.01, US-8.02                                              |
| S-013  | F35, F36, F37              | US-8.03, US-8.04, US-8.05, US-8.06, US-8.07                   |
| S-014  | F41                        | US-9.05, US-9.04, US-8.10                                     |
| S-015  | F42, F43                   | US-10.01, US-10.02, US-10.12, US-10.13, US-10.08              |
| S-016  | F28                        | US-6.01                                                       |
| S-017  | F28                        | US-6.02, US-6.04                                              |
| S-018  | F29, F30                   | US-6.04, US-6.05, US-6.06, US-6.09                            |
| S-019  | F22, F29                   | US-4.02, US-4.03, US-4.06, US-6.05                            |
| S-020  | F25                        | US-5.02, US-5.03, US-5.04                                     |
