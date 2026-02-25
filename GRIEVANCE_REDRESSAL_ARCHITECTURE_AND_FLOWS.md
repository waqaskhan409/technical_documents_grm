# Grievance Redressal Mechanism (GRM) — Architecture & Flow Diagrams

This document provides technical architecture and process flow diagrams for the GRM platform.

## 1) High-Level System Architecture

```mermaid
flowchart LR
  subgraph Channels[Complaint Sources]
    MA[Mobile App]
    WF[Web Form / Portal]
    CC[Call Centre / IVR]
    HF[Hardcopy Forms]
    EM[Email / Chat / SMS]
    SM[Social / Kiosk]
  end

  subgraph Ingestion[Intake & Normalization Layer]
    API[API Gateway]
    ETL[Validation + Normalization]
    DQ[Data Quality Checks]
  end

  subgraph Core[GRM Core Services]
    CRS[Complaint Registration Service]
    DEDUPE[Deduplication & Similarity Engine]
    GMS[Grievance Management Service]
    WFE[Workflow & Assignment Engine]
    SLA[SLA & Escalation Engine]
    COMMS[Notification Service]
    AUDIT[Audit & Compliance Service]
  end

  subgraph Data[Data Layer]
    CDB[(Complaints DB)]
    GDB[(Grievances DB)]
    ADB[(Audit Logs)]
    FDB[(Files/Attachments Store)]
    RPT[(Analytics / BI Store)]
  end

  subgraph Ops[Operations & Governance]
    DASH[Operations Dashboard]
    GOV[Governance Committee View]
    CSAT[Citizen Feedback / CSAT]
  end

  MA --> API
  WF --> API
  CC --> API
  HF --> API
  EM --> API
  SM --> API

  API --> ETL --> DQ --> CRS
  CRS --> DEDUPE --> GMS
  GMS --> WFE --> SLA
  GMS --> COMMS
  GMS --> AUDIT

  CRS --> CDB
  GMS --> GDB
  AUDIT --> ADB
  CRS --> FDB
  GMS --> FDB

  CDB --> RPT
  GDB --> RPT
  ADB --> RPT

  RPT --> DASH
  RPT --> GOV
  COMMS --> CSAT
```

## 2) Complaint to Grievance Consolidation Flow

```mermaid
flowchart TD
  A[Complaint Received] --> B[Validate Required Fields]
  B --> C{Valid}
  C -- No --> C1[Return For Correction]
  C -- Yes --> D[Register Complaint And Acknowledge]
  D --> E[Classify Category Location Department]
  E --> F[Run Duplicate Similarity Check]

  F --> G{Existing Grievance Match}
  G -- High --> H[Auto Link To Grievance]
  G -- Medium --> I[Send To Triage Queue]
  G -- None --> J[Create New Grievance]

  I --> K{Analyst Decision}
  K -- Link --> H
  K -- New --> J

  H --> L[Update Grievance Metrics]
  J --> L
  L --> M[Assign Owner And Start SLA]
  M --> N[Notify Complainants]
```

## 3) End-to-End Grievance Redressal Workflow

```mermaid
flowchart TD
  N1[New Complaints] --> N2[Grievance Created Or Linked]
  N2 --> N3[Assigned To Department]
  N3 --> N4[Investigation And Evidence Collection]
  N4 --> N5{Resolution Proposed}

  N5 -- No --> N4
  N5 -- Yes --> N6[Supervisor And Nodal Review]
  N6 --> N7{Approved}
  N7 -- No --> N4
  N7 -- Yes --> N8[Resolution Communicated]
  N8 --> N9[Closure With Proof]

  N9 --> N10{Appeal Or Reopen Within Window}
  N10 -- Yes --> N11[Reopen And Reinvestigate]
  N11 --> N4
  N10 -- No --> N12[Final Closure And Feedback]
```

## 4) SLA Monitoring and Escalation Flow

```mermaid
flowchart TD
  S1[Case Assigned] --> S2[Start SLA Clock]
  S2 --> S3{First Response Due?}
  S3 -- Missed --> E1[Escalate to L2 Supervisor]
  S3 -- Met --> S4[Continue Investigation]

  S4 --> S5{Resolution Due?}
  S5 -- Missed --> E2[Escalate to L3 Nodal Officer]
  E2 --> S6{Still Overdue / High Sensitivity?}
  S6 -- Yes --> E3[Escalate to L4 Committee/Ombudsperson]
  S6 -- No --> S7[Action Plan & Recovery SLA]

  S5 -- Met --> S8[Resolve & Close]
  E1 --> S4
  E3 --> S7
  S7 --> S8
```

## 5) Role-Based Responsibility Flow (RACI Style)

```mermaid
flowchart LR
  IA[Intake Agent] --> TA[Triage Analyst]
  TA --> CO[Case Officer]
  CO --> SP[Supervisor]
  SP --> NO[Nodal Officer]
  NO --> GC[Grievance Committee]

  IA -. registers .-> C1[(Complaint)]
  TA -. classifies/links .-> G1[(Grievance)]
  CO -. investigates/resolves .-> G1
  SP -. approves/escalates .-> G1
  NO -. cross-dept oversight .-> G1
  GC -. appeal/adjudication .-> G1
```

## 6) Logical Data Model (Entity Relationship)

```mermaid
erDiagram
  COMPLAINTS ||--o{ GRIEVANCE_COMPLAINT_LINKS : linked_to
  GRIEVANCES ||--o{ GRIEVANCE_COMPLAINT_LINKS : contains
  GRIEVANCES ||--o{ GRIEVANCE_ACTIONS : has
  GRIEVANCES ||--o{ ESCALATIONS : triggers
  GRIEVANCES ||--o{ COMMUNICATIONS : sends
  GRIEVANCES ||--o{ ATTACHMENTS : stores
  COMPLAINTS ||--o{ ATTACHMENTS : stores
  USERS ||--o{ GRIEVANCE_ACTIONS : performs
  DEPARTMENTS ||--o{ GRIEVANCES : owns
  GRIEVANCES ||--o{ AUDIT_LOGS : audited_in
  COMPLAINTS ||--o{ AUDIT_LOGS : audited_in
```

## 7) Status Lifecycle State Diagram

```mermaid
stateDiagram-v2
  [*] --> NEW
  NEW --> ACKNOWLEDGED
  ACKNOWLEDGED --> TRIAGED
  TRIAGED --> ASSIGNED
  ASSIGNED --> IN_PROGRESS
  IN_PROGRESS --> RESOLVED
  RESOLVED --> CLOSED

  CLOSED --> REOPEN_REQUESTED
  REOPEN_REQUESTED --> REOPENED
  REOPENED --> IN_PROGRESS
  IN_PROGRESS --> CLOSED: Duplicate/Invalid with approval
```

## 8) Deployment View (Reference)

```mermaid
flowchart TB
  subgraph Edge[Edge]
    CDN[CDN / WAF]
    LB[Load Balancer]
  end

  subgraph App[Application Tier]
    FE[Frontend Web + Mobile APIs]
    BE[Backend GRM Services]
    MQ[Queue / Event Bus]
  end

  subgraph DataTier[Data Tier]
    SQL[(Primary SQL DB)]
    CACHE[(Redis Cache)]
    OBJ[(Object Storage)]
    BI[(Analytics Warehouse)]
  end

  subgraph Integrations[External Integrations]
    SMS[SMS/Email Gateway]
    CRM[CRM/ERP]
    IAM[Identity Provider]
  end

  CDN --> LB --> FE --> BE
  BE --> SQL
  BE --> CACHE
  BE --> OBJ
  BE --> MQ --> BI
  BE --> SMS
  BE --> CRM
  FE --> IAM
```

## 9) Notes for Implementation

- Use these Mermaid blocks directly in Markdown-compatible viewers (GitHub, GitLab, many docs portals).
- If your platform does not support Mermaid natively, export diagrams to SVG/PNG during CI and embed images.
- Keep diagram labels aligned with your actual module/service names to avoid drift.

## 10) Government Governance and Escalation View

```mermaid
flowchart TB
  C[Citizen Complaint Intake]
  FO[Field Office And DGO]
  DO[District Grievance Cell]
  SO[State Or Head Office Nodal Team]
  AA[Appellate Authority]
  RC[Review Committee]
  SEC[Department Secretary Or Competent Authority]

  C --> FO
  FO -->|SLA Breach Or Complex Case| DO
  DO -->|Unresolved Or Inter District| SO
  SO -->|Appeal Or High Sensitivity| AA
  AA --> RC
  RC --> SEC

  FO -->|Monthly Disposal Report| RC
  DO -->|Pendency And SLA Report| RC
  SO -->|State Dashboard And Alerts| RC
```
