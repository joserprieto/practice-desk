# Personas

User archetypes for Practice Desk, a management platform for a Spanish law firm (bufete de
abogados). Each persona represents a distinct role with specific goals, pain points, and interaction
patterns within the legal domain.

## P1: Managing Partner (Socio Director)

**Role:** Licensed attorney (numero de colegiado) who owns or co-owns the firm. Oversees firm
operations, reviews invoices, monitors deadlines across the firm, and evaluates associate
performance (billable hours, case outcomes).

**Demographics:** 45-60 years old, 20+ years of legal experience, manages 2-15 professionals in the
firm.

**Goals:**

- Real-time operational dashboard with firm-wide visibility
- Firm-wide deadline alerts to ensure zero missed plazos procesales
- One-click invoice approval before sending to clients
- Profitability reports by client, area of law, and associate
- Monitor workload distribution and reassign expedientes when associates are overloaded
- Track financial KPIs: revenue, utilization rate, realization rate, collection rate
- Ensure RGPD compliance across all client data

**Pain points:**

- No visibility into firm-wide deadline compliance across all expedientes
- Cannot see which associates are overloaded until problems surface
- Invoice approval is manual and slow, requiring paper or email roundtrips
- No profitability data broken down by client or area of law
- Client satisfaction issues surface too late because of fragmented information

**Usage pattern:** Checks the partner dashboard Monday morning and 2-3 times daily. Reviews
expedientes with upcoming deadlines, approves invoices, reassigns cases between associates. Mostly
reads and approves; occasionally overrides or reassigns. Uses desktop primarily.

**Scenario:** Arrives Monday morning, checks the partner dashboard. Sees 3 expedientes with
deadlines this week, 2 overdue document requests, and an invoice pending approval. Reassigns one
overloaded associate's expediente to another. Reviews and approves two invoices before sending to
clients.

**Key metric:** Zero missed deadlines; invoice approval turnaround under 24 hours.

---

## P2: Associate (Abogado Asociado)

**Role:** Licensed attorney with active caseload. Primary daily user of the platform. Manages
expedientes end-to-end: creates cases, registers parties and courts, tracks procedural phases,
records actuaciones procesales, manages plazos procesales, schedules hearings (vistas), communicates
with clients and procurador, drafts legal documents, and tracks billable time.

**Demographics:** 28-45 years old, 3-15 years of experience, manages 10-30 active expedientes at any
given time.

**Goals:**

- Never miss a deadline: automatic calculation of plazos procesales counting dias habiles, with
  escalating alerts
- All case information in one place: parties, courts, documents, communications, billing
- Efficient document drafting from reusable templates
- Easy time tracking linked directly to expedientes
- Quick access to precedents from similar past cases

**Pain points:**

- Terrified of missing a plazo procesal, which causes preclusion, caducidad, or professional
  liability
- Manually calculates deadlines counting dias habiles, a tedious and error-prone process
- Documents scattered across email, WhatsApp, and paper files
- Cannot easily find precedents from similar past cases within the firm
- Time tracking is an afterthought, leading to lost billable hours

**Usage pattern:** Works in the system all day. Creates expedientes, manages plazos, requests
documents from clients, records actuaciones, schedules vistas, logs billable hours, and drafts
documents. Uses both desktop and mobile.

**Scenario:** Opens the associate dashboard. Has 12 active expedientes. One has a plazo procesal
expiring in 3 days (contestacion a la demanda). Another has a vista scheduled tomorrow at Juzgado de
Primera Instancia n.3 de Madrid. Needs to request documents from a client for a new case. Records
2.5 hours of billable time on a contract review.

**Key metric:** Zero missed plazos procesales; billable hours accurately captured; documents
collected on time.

---

## P3: Paralegal (Auxiliar Juridico / Secretario/a)

**Role:** Legal support staff assisting one or more associates. Handles document management,
deadline tracking, appointment scheduling, invoice generation, and client communication. Monitors
incoming court notifications. Maintains court directory (juzgados) and procurador contacts.
Coordinates hearing logistics with clients, procurador, and witnesses.

**Demographics:** 25-40 years old, administrative and organizational focus, strong knowledge of
court procedures and legal terminology.

**Goals:**

- Combined deadline calendar showing plazos for all supported associates in one view
- Automated invoice calculations handling IVA, IRPF, honorarios, and suplidos correctly
- Centralized court (juzgado) and procurador directory replacing scattered spreadsheets
- Document filing tracking, including submissions via LexNET
- Efficient coordination of hearing logistics across multiple cases

**Pain points:**

- Supports 3 associates with different working styles and no centralized deadline view across them
- Invoice generation requires manual calculation of IVA and IRPF, separating honorarios from
  suplidos
- Court directory is maintained in a spreadsheet that quickly becomes outdated
- Tracking which documents have been filed with the court and which are pending is error-prone

**Usage pattern:** Heavy daily user. Manages deadline calendars, generates invoices, schedules
client meetings and hearings, files documents with courts. Desktop-first with occasional mobile for
quick checks.

**Scenario:** Checks the deadline calendar for all associates she supports. Sends a reminder to an
associate about a plazo expiring in 2 days. Schedules a client meeting for an associate. Generates
an invoice for a completed expediente, separating honorarios from suplidos. Files a document to the
court via LexNET.

**Key metric:** Deadline reminder compliance rate; invoice accuracy; document filing completion
rate.

---

## P4: Client (Cliente)

**Role:** External user. Individual or company receiving legal services from the firm. Interacts
through a client portal to view case status, upload documents, receive notifications, communicate
with their lawyer, and review invoices.

**Demographics:** Varies widely. May or may not be tech-savvy. Often non-technical and uses phone
primarily. Interacts with the firm infrequently but cares deeply about their case.

**Goals:**

- Clear case status without having to call the office
- Simple document upload from phone, with a checklist of what is needed
- Hearing notifications including date, location, and what to prepare
- Transparent billing so they understand what they are paying for

**Pain points:**

- Does not know what is happening with their case and has to call the office to ask
- Does not understand legal jargon used in status updates or documents
- Overwhelmed by document requests without clear instructions on what to provide
- Does not know what they are paying for or how fees are calculated
- Lost in email threads and unable to find previously shared documents

**Usage pattern:** Sporadic. Logs in when they receive a notification (document request, hearing
reminder, status update). Primarily mobile. Needs the simplest possible UX with plain-language
explanations.

**Scenario:** Receives notification: "Your lawyer has requested 3 documents for your case." Opens
client portal on phone. Sees checklist: DNI (uploaded), poder notarial (pending), contrato de
arrendamiento (pending). Takes a photo of the contrato and uploads it. Checks case status: "En
tramite - Audiencia previa programada 15/03/2026." Sees next hearing date and location.

**Key metric:** Self-service rate (percentage of document submissions without firm intervention);
client satisfaction score.

---

## P5: Office Administrator (Administrador/a de Oficina)

**Role:** Manages firm configuration and operations. Handles user accounts and roles, client
registry (NIF/CIF, contact information), court directory data, and notification settings. Generates
management reports and imports data from legacy systems (CSV/Excel). In smaller firms, this role may
overlap with the paralegal.

**Demographics:** 30-50 years old, operations and administration background, familiar with legal
firm workflows.

**Goals:**

- Bulk data import for client migration from legacy systems
- Easy configuration of user accounts, roles, and permissions
- Automated reporting (monthly billing, matter volume, resolution times)
- Centralized administration of firm-wide settings and directories

**Pain points:**

- Manual data entry for client migration from old systems is slow and error-prone
- Court information (juzgado addresses, phone numbers) changes without notice
- Report generation requires exporting to Excel and manual formatting
- Onboarding new staff requires teaching multiple disconnected tools

**Usage pattern:** Administrative tasks throughout the week. Handles user onboarding, configuration
changes, data imports, and weekly or monthly reporting. Desktop only.

**Scenario:** Onboards a new associate: creates account, assigns role, sets permissions. Imports 50
clients from old system via CSV. Updates court directory after a juzgado changes address. Generates
monthly billing report for the managing partner.

**Key metric:** Operational efficiency; data import accuracy; report generation turnaround.

---

## Persona Priority

| Persona                          | Priority | Rationale                                                     |
| -------------------------------- | -------- | ------------------------------------------------------------- |
| P2: Associate (Abogado Asociado) | Critical | Primary daily user, drives all core legal workflows           |
| P4: Client (Cliente)             | Critical | External-facing, document collection and satisfaction depend  |
| P1: Managing Partner (Socio Dir) | High     | Decision-maker, needs oversight dashboard and invoice control |
| P3: Paralegal (Aux. Juridico)    | High     | Key support role, handles deadline tracking and invoicing     |
| P5: Office Admin (Admin.)        | Medium   | Important but can be deferred to later phases                 |
