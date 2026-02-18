# Feature Inventory — Legal Practice Management

Scope: **Legal vertical only** (see
[ADR-0005](../../architecture/adrs/ADR-0005-legal-vertical-scope.md)).

Features mapped to [user stories](../user-stories/README.md) across 10 epics. Priority: **Must**
(UMFP), **Should** (post-UMFP), **Could** (future).

## Feature Table

| ID  | Feature                              | Epic  | Priority | Key Stories                     |
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
| F11 | Deadline Registration & Calculation  | EP-02 | Must     | US-2.01, 2.02, 2.08             |
| F12 | Deadline Alert System                | EP-02 | Must     | US-2.03, 2.07, 2.16             |
| F13 | Deadline Calendar                    | EP-02 | Must     | US-2.04, 2.15                   |
| F14 | Deadline Dashboard & Compliance      | EP-02 | Must     | US-2.06, 2.10                   |
| F15 | Actuacion Procesal Recording         | EP-03 | Must     | US-3.01, 3.02                   |
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
| F29 | Client Portal Documents              | EP-06 | Must     | US-6.05                         |
| F30 | Client Portal Invoices               | EP-06 | Should   | US-6.09, 6.12                   |
| F31 | Appointment Scheduling               | EP-07 | Must     | US-7.01, 7.02, 7.04, 7.05, 7.06 |
| F32 | Appointment Calendar                 | EP-07 | Must     | US-7.03, 7.07, 7.09             |
| F33 | Appointment Reminders                | EP-07 | Should   | US-7.08, 7.10                   |
| F34 | Time Tracking                        | EP-08 | Must     | US-8.01, 8.02                   |
| F35 | Invoice Generation (VeriFactu)       | EP-08 | Must     | US-8.03, 8.04, 8.12             |
| F36 | Payment Tracking                     | EP-08 | Must     | US-8.05, 8.08, 8.18             |
| F37 | Provision de Fondos & Suplidos       | EP-08 | Must     | US-8.06, 8.07                   |
| F38 | Billing Models & Profitability       | EP-08 | Should   | US-8.10, 8.11, 8.13             |
| F39 | Partner Dashboard                    | EP-09 | Must     | US-9.01, 9.03                   |
| F40 | Associate Dashboard                  | EP-09 | Must     | US-9.02                         |
| F41 | Reports & Export                     | EP-09 | Should   | US-9.04, 9.05, 9.07             |
| F42 | Firm Configuration & User Management | EP-10 | Must     | US-10.01, 10.02, 10.03, 10.05   |
| F43 | Audit Trail & RGPD                   | EP-10 | Must     | US-10.04, 10.08                 |

## Summary by Epic

| Epic                             | Features |  Must  | Should | Could |
| -------------------------------- | :------: | :----: | :----: | :---: |
| EP-01: Expediente Management     |    10    |   7    |   3    |   0   |
| EP-02: Procedural Deadlines      |    4     |   4    |   0    |   0   |
| EP-03: Proceedings & Hearings    |    6     |   4    |   2    |   0   |
| EP-04: Document Management       |    4     |   3    |   1    |   0   |
| EP-05: Client Communication      |    3     |   2    |   1    |   0   |
| EP-06: Client Portal             |    3     |   2    |   1    |   0   |
| EP-07: Appointment Scheduling    |    3     |   2    |   1    |   0   |
| EP-08: Financial Management      |    5     |   3    |   2    |   0   |
| EP-09: Dashboard & Reporting     |    3     |   2    |   1    |   0   |
| EP-10: Administration & Security |    2     |   2    |   0    |   0   |
| **Total**                        |  **43**  | **31** | **12** | **0** |

## UMFP Scope

31 Must features spanning all 10 epics form the UMFP scope:

- **EP-01** — F01, F02, F03, F04, F05, F06, F07
- **EP-02** — F11, F12, F13, F14
- **EP-03** — F15, F16, F17, F18
- **EP-04** — F21, F22, F23
- **EP-05** — F25, F26
- **EP-06** — F28, F29
- **EP-07** — F31, F32
- **EP-08** — F34, F35, F36, F37
- **EP-09** — F39, F40
- **EP-10** — F42, F43

## Traceability: Feature to Stories

| Feature                                    | Stories                                     |
| ------------------------------------------ | ------------------------------------------- |
| F01 — Expediente CRUD & Lifecycle          | US-1.01, US-1.05, US-1.06                   |
| F02 — Expediente Classification            | US-1.11, US-1.19                            |
| F03 — Party Management                     | US-1.03, US-1.16                            |
| F04 — Court & Case Number Assignment       | US-1.04                                     |
| F05 — Procurador Assignment                | US-1.02                                     |
| F06 — Conflict of Interest Check           | US-1.09                                     |
| F07 — Expediente Workload View             | US-1.05, US-1.07, US-1.08, US-1.10          |
| F08 — Expediente Chain Linking             | US-1.12                                     |
| F09 — Hoja de Encargo Management           | US-1.13, US-6.11                            |
| F10 — Expediente Audit Timeline            | US-1.14                                     |
| F11 — Deadline Registration & Calculation  | US-2.01, US-2.02, US-2.08                   |
| F12 — Deadline Alert System                | US-2.03, US-2.07, US-2.16                   |
| F13 — Deadline Calendar                    | US-2.04, US-2.15                            |
| F14 — Deadline Dashboard & Compliance      | US-2.06, US-2.10                            |
| F15 — Actuacion Procesal Recording         | US-3.01, US-3.02                            |
| F16 — Proceeding Management                | US-3.08, US-3.11, US-3.12                   |
| F17 — Hearing Scheduling & Coordination    | US-3.03, US-3.04, US-3.07                   |
| F18 — Resolution Tracking                  | US-3.05, US-3.06                            |
| F19 — Hearing Preparation Checklist        | US-3.09, US-3.10                            |
| F20 — Court Directory                      | US-3.13, US-10.06                           |
| F21 — Document Request & Collection        | US-4.01, US-4.02, US-4.04, US-4.07          |
| F22 — Document Upload & Review             | US-4.03, US-4.05, US-4.06                   |
| F23 — Document Organization                | US-4.08, US-4.13                            |
| F24 — Document Templates & Generation      | US-4.10, US-4.12                            |
| F25 — Client Messaging                     | US-5.01, US-5.02, US-5.03, US-5.04          |
| F26 — Case Update Notifications            | US-5.05, US-5.10                            |
| F27 — Message Templates                    | US-5.08                                     |
| F28 — Client Portal Dashboard              | US-6.01, US-6.02, US-6.03, US-6.04          |
| F29 — Client Portal Documents              | US-6.05                                     |
| F30 — Client Portal Invoices               | US-6.09, US-6.12                            |
| F31 — Appointment Scheduling               | US-7.01, US-7.02, US-7.04, US-7.05, US-7.06 |
| F32 — Appointment Calendar                 | US-7.03, US-7.07, US-7.09                   |
| F33 — Appointment Reminders                | US-7.08, US-7.10                            |
| F34 — Time Tracking                        | US-8.01, US-8.02                            |
| F35 — Invoice Generation (VeriFactu)       | US-8.03, US-8.04, US-8.12                   |
| F36 — Payment Tracking                     | US-8.05, US-8.08, US-8.18                   |
| F37 — Provision de Fondos & Suplidos       | US-8.06, US-8.07                            |
| F38 — Billing Models & Profitability       | US-8.10, US-8.11, US-8.13                   |
| F39 — Partner Dashboard                    | US-9.01, US-9.03                            |
| F40 — Associate Dashboard                  | US-9.02                                     |
| F41 — Reports & Export                     | US-9.04, US-9.05, US-9.07                   |
| F42 — Firm Configuration & User Management | US-10.01, US-10.02, US-10.03, US-10.05      |
| F43 — Audit Trail & RGPD                   | US-10.04, US-10.08                          |

## Traceability: Story to Feature (reverse)

### EP-01: Expediente Management

| Story   | Feature                                                           |
| ------- | ----------------------------------------------------------------- |
| US-1.01 | F01 — Expediente CRUD & Lifecycle                                 |
| US-1.02 | F05 — Procurador Assignment                                       |
| US-1.03 | F03 — Party Management                                            |
| US-1.04 | F04 — Court & Case Number Assignment                              |
| US-1.05 | F01 — Expediente CRUD & Lifecycle, F07 — Expediente Workload View |
| US-1.06 | F01 — Expediente CRUD & Lifecycle                                 |
| US-1.07 | F07 — Expediente Workload View                                    |
| US-1.08 | F07 — Expediente Workload View                                    |
| US-1.09 | F06 — Conflict of Interest Check                                  |
| US-1.10 | F07 — Expediente Workload View                                    |
| US-1.11 | F02 — Expediente Classification                                   |
| US-1.12 | F08 — Expediente Chain Linking                                    |
| US-1.13 | F09 — Hoja de Encargo Management                                  |
| US-1.14 | F10 — Expediente Audit Timeline                                   |
| US-1.16 | F03 — Party Management                                            |
| US-1.19 | F02 — Expediente Classification                                   |

### EP-02: Procedural Deadlines

| Story   | Feature                                   |
| ------- | ----------------------------------------- |
| US-2.01 | F11 — Deadline Registration & Calculation |
| US-2.02 | F11 — Deadline Registration & Calculation |
| US-2.03 | F12 — Deadline Alert System               |
| US-2.04 | F13 — Deadline Calendar                   |
| US-2.06 | F14 — Deadline Dashboard & Compliance     |
| US-2.07 | F12 — Deadline Alert System               |
| US-2.08 | F11 — Deadline Registration & Calculation |
| US-2.10 | F14 — Deadline Dashboard & Compliance     |
| US-2.15 | F13 — Deadline Calendar                   |
| US-2.16 | F12 — Deadline Alert System               |

### EP-03: Proceedings & Hearings

| Story   | Feature                                 |
| ------- | --------------------------------------- |
| US-3.01 | F15 — Actuacion Procesal Recording      |
| US-3.02 | F15 — Actuacion Procesal Recording      |
| US-3.03 | F17 — Hearing Scheduling & Coordination |
| US-3.04 | F17 — Hearing Scheduling & Coordination |
| US-3.05 | F18 — Resolution Tracking               |
| US-3.06 | F18 — Resolution Tracking               |
| US-3.07 | F17 — Hearing Scheduling & Coordination |
| US-3.08 | F16 — Proceeding Management             |
| US-3.09 | F19 — Hearing Preparation Checklist     |
| US-3.10 | F19 — Hearing Preparation Checklist     |
| US-3.11 | F16 — Proceeding Management             |
| US-3.12 | F16 — Proceeding Management             |
| US-3.13 | F20 — Court Directory                   |

### EP-04: Document Management

| Story   | Feature                               |
| ------- | ------------------------------------- |
| US-4.01 | F21 — Document Request & Collection   |
| US-4.02 | F21 — Document Request & Collection   |
| US-4.03 | F22 — Document Upload & Review        |
| US-4.04 | F21 — Document Request & Collection   |
| US-4.05 | F22 — Document Upload & Review        |
| US-4.06 | F22 — Document Upload & Review        |
| US-4.07 | F21 — Document Request & Collection   |
| US-4.08 | F23 — Document Organization           |
| US-4.10 | F24 — Document Templates & Generation |
| US-4.12 | F24 — Document Templates & Generation |
| US-4.13 | F23 — Document Organization           |

### EP-05: Client Communication

| Story   | Feature                         |
| ------- | ------------------------------- |
| US-5.01 | F25 — Client Messaging          |
| US-5.02 | F25 — Client Messaging          |
| US-5.03 | F25 — Client Messaging          |
| US-5.04 | F25 — Client Messaging          |
| US-5.05 | F26 — Case Update Notifications |
| US-5.08 | F27 — Message Templates         |
| US-5.10 | F26 — Case Update Notifications |

### EP-06: Client Portal

| Story   | Feature                          |
| ------- | -------------------------------- |
| US-6.01 | F28 — Client Portal Dashboard    |
| US-6.02 | F28 — Client Portal Dashboard    |
| US-6.03 | F28 — Client Portal Dashboard    |
| US-6.04 | F28 — Client Portal Dashboard    |
| US-6.05 | F29 — Client Portal Documents    |
| US-6.09 | F30 — Client Portal Invoices     |
| US-6.11 | F09 — Hoja de Encargo Management |
| US-6.12 | F30 — Client Portal Invoices     |

### EP-07: Appointment Scheduling

| Story   | Feature                      |
| ------- | ---------------------------- |
| US-7.01 | F31 — Appointment Scheduling |
| US-7.02 | F31 — Appointment Scheduling |
| US-7.03 | F32 — Appointment Calendar   |
| US-7.04 | F31 — Appointment Scheduling |
| US-7.05 | F31 — Appointment Scheduling |
| US-7.06 | F31 — Appointment Scheduling |
| US-7.07 | F32 — Appointment Calendar   |
| US-7.08 | F33 — Appointment Reminders  |
| US-7.09 | F32 — Appointment Calendar   |
| US-7.10 | F33 — Appointment Reminders  |

### EP-08: Financial Management

| Story   | Feature                              |
| ------- | ------------------------------------ |
| US-8.01 | F34 — Time Tracking                  |
| US-8.02 | F34 — Time Tracking                  |
| US-8.03 | F35 — Invoice Generation (VeriFactu) |
| US-8.04 | F35 — Invoice Generation (VeriFactu) |
| US-8.05 | F36 — Payment Tracking               |
| US-8.06 | F37 — Provision de Fondos & Suplidos |
| US-8.07 | F37 — Provision de Fondos & Suplidos |
| US-8.08 | F36 — Payment Tracking               |
| US-8.10 | F38 — Billing Models & Profitability |
| US-8.11 | F38 — Billing Models & Profitability |
| US-8.12 | F35 — Invoice Generation (VeriFactu) |
| US-8.13 | F38 — Billing Models & Profitability |
| US-8.18 | F36 — Payment Tracking               |

### EP-09: Dashboard & Reporting

| Story   | Feature                   |
| ------- | ------------------------- |
| US-9.01 | F39 — Partner Dashboard   |
| US-9.02 | F40 — Associate Dashboard |
| US-9.03 | F39 — Partner Dashboard   |
| US-9.04 | F41 — Reports & Export    |
| US-9.05 | F41 — Reports & Export    |
| US-9.07 | F41 — Reports & Export    |

### EP-10: Administration & Security

| Story    | Feature                                    |
| -------- | ------------------------------------------ |
| US-10.01 | F42 — Firm Configuration & User Management |
| US-10.02 | F42 — Firm Configuration & User Management |
| US-10.03 | F42 — Firm Configuration & User Management |
| US-10.04 | F43 — Audit Trail & RGPD                   |
| US-10.05 | F42 — Firm Configuration & User Management |
| US-10.06 | F20 — Court Directory                      |
| US-10.08 | F43 — Audit Trail & RGPD                   |
