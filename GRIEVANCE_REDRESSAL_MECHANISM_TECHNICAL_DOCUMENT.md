# Grievance Redressal Mechanism (GRM) — Technical Document

## 1) Purpose

This document defines a production-ready **Grievance Redressal Mechanism (GRM)** where:

- Complaints are received from multiple channels.
- Multiple related complaints are consolidated into a single grievance case.
- Cases are resolved through standardized workflows, SLA controls, escalation, and auditability.

It is designed for digital + assisted + offline intake and can be adapted for public sector, enterprise, or NGO grievance operations.

## 2) Scope

The mechanism covers:

- Intake from all major complaint sources.
- Validation, deduplication, and complaint-to-grievance linking.
- Automated and manual triage.
- Assignment, investigation, resolution, closure, and reopening.
- Multi-level escalation and oversight committees.
- Citizen communication, transparency, and analytics.
- Security, privacy, and compliance controls.

### 2.1 Assumptions and Boundaries

- This document defines the target operating model and technical controls for GRM, not vendor-specific implementation code.
- Department-specific legal clauses, compensation rules, and appeal timelines must be parameterized in policy configuration.
- Channel adapters (mobile, call centre, hardcopy entry, web) are expected to integrate through normalized intake APIs.
- Integration with IAM, SMS/email gateways, and analytics stack is assumed available in production.

## 3) Core Definitions

- **Complaint**: A single submission by a citizen/public service beneficiary regarding an issue.
- **Grievance**: A managed case representing one issue that may include one or more related complaints.
- **Complainant**: Individual/entity raising a complaint.
- **Case Owner**: Officer/team accountable for grievance resolution.
- **SLA**: Target timelines for acknowledgement, first response, and final resolution.
- **Escalation**: Automatic/manual routing to higher authority on SLA breach or severity.

### 3.1 Government Context Terms

- **Public Authority/Department**: Administrative unit responsible for service delivery and grievance disposal.
- **Designated Grievance Officer (DGO)**: Official designated to supervise registration, tracking, and timely disposal.
- **Appellate Authority**: Higher authority to hear appeals/reopen requests as per applicable rules.
- **Citizen Charter Alignment**: Mapping service categories to published standards and response timelines.

## 4) Multi-Channel Complaint Sources

The system must support all channels below with normalized ingestion:

1. **Mobile App** (Android/iOS)
2. **Web Forms / Portal**
3. **Call Centre / IVR / Agent Desktop**
4. **Hardcopy Forms** (counter/field office/back-office data entry)
5. **Email Inbox** (parser + manual verification)
6. **SMS/WhatsApp/Chatbot** (optional but recommended)
7. **Social Media Mentions** (optional listening + ticket conversion)
8. **Kiosk / Help Desk**

### 4.1 Channel-Specific Requirements

- Every channel must generate a unique `complaint_id`.
- All inputs must map to a standard schema.
- Channel metadata must be captured (`source_channel`, `agent_id`, `location`, `capture_mode`).
- Offline channels (hardcopy/call) require data-entry quality checks and scan attachments.

## 5) Complaint-to-Grievance Conversion Logic

## 5.1 Why Many Complaints Become One Grievance

Multiple citizens may report the same root problem (e.g., water outage in area X). To avoid duplicate effort, related complaints are grouped under one grievance while preserving each complaint’s identity and communication trail.

## 5.2 Consolidation Rules

Create or link to a grievance using rule engine + analyst review:

- Same issue category/subcategory.
- Same location/geography (ward/zone/district).
- Similar title/description (NLP similarity threshold).
- Similar incident date window.
- Same responsible department.

If confidence >= threshold (e.g., 0.85), auto-link complaint to existing grievance; else send to triage queue.

## 5.3 Relationship Model

- One grievance can have many complaints.
- One complaint belongs to one active grievance at a time.
- Complaints retain individual status messaging even when grouped.

## 6) End-to-End Workflow

1. **Intake**
   - Receive complaint from channel.
   - Validate mandatory fields and consent.
2. **Registration**
   - Generate acknowledgement ID + timestamp.
   - Notify complainant (SMS/email/app push/printed receipt).
3. **Screening & Classification**
   - Category, severity, impact, jurisdiction check.
4. **Deduplication & Grouping**
   - Link to existing grievance or create new grievance.
5. **Assignment**
   - Route to department/officer by rules matrix.
6. **Investigation**
   - Evidence collection, field verification, inter-department tasks.
7. **Resolution Proposal**
   - Corrective action, compensation/remedy, preventive controls.
8. **Approval & Communication**
   - Supervisor approval where required.
   - Share resolution with all linked complainants.
9. **Closure**
   - Capture closure reason and proof.
10. **Reopen / Appeal**

- Allow within defined window (e.g., 15–30 days).

## 7) SLA and Escalation Framework

## 7.1 Suggested SLA Matrix

| Priority | Example                                      | Acknowledgement | First Response | Resolution Target |
| -------- | -------------------------------------------- | --------------: | -------------: | ----------------: |
| Critical | Safety, legal risk, essential service outage |          15 min |          2 hrs |            24 hrs |
| High     | Major service impact                         |            1 hr |          8 hrs |            3 days |
| Medium   | Standard service complaint                   |           4 hrs |          1 day |            7 days |
| Low      | Information/low impact issue                 |           1 day |         2 days |           15 days |

## 7.2 Escalation Levels

- **L1**: Case Officer / Frontline Team
- **L2**: Department Supervisor
- **L3**: Nodal Officer / Regional Head
- **L4**: Grievance Committee / Ombudsperson / Executive Authority

Escalation triggers:

- SLA breach at any stage.
- Repeated reopenings.
- High media/public sensitivity.
- Policy/legal category flags.

### 7.3 Government Escalation Mapping (Recommended)

- **Administrative Ladder**: Field Office -> District/Regional Office -> State/Head Office -> Department Secretary/Competent Authority.
- **Time-Bound Escalation**: Auto-escalation after SLA breach with mandatory reason capture and action plan.
- **Appellate Workflow**: First appeal and second appeal paths configurable by department rules.
- **Special Category Routing**: Priority handling for vulnerable citizens, essential services, and statutory deadline cases.

## 8) Resolution Mechanisms (All Types)

The framework supports multiple resolution paths:

1. **Direct Administrative Resolution**
   - Department fixes issue directly.
2. **Service Recovery Resolution**
   - Restore service + preventive fix.
3. **Compensatory Resolution**
   - Refund/credit/compensation where policy allows.
4. **Mediation / Conciliation**
   - Facilitated agreement between complainant and service unit.
5. **Committee-Based Resolution**
   - Cases reviewed by grievance committee for complex matters.
6. **Legal/Regulatory Referral**
   - Escalate to legal cell or statutory body when required.
7. **Policy Exception Review**
   - Senior authority reviews exceptional requests.
8. **Systemic Corrective Action**
   - Root-cause elimination for recurring complaints.

## 9) Governance and Roles

| Role                     | Responsibilities                                       |
| ------------------------ | ------------------------------------------------------ |
| GRM Administrator        | Configures workflows, categories, SLAs, users          |
| Intake Agent             | Registers complaints from assisted/offline channels    |
| Triage Analyst           | Classification, deduplication, grievance linking       |
| Case Officer             | Investigates and executes resolution                   |
| Supervisor               | Quality checks, approval, escalations                  |
| Nodal Officer            | Cross-department coordination, high-priority oversight |
| Grievance Committee      | Complex/appeal case adjudication                       |
| Audit/Compliance Officer | Monitors policy, evidence, and audit integrity         |

### 9.1 Government Governance Model

- Department order/notification should define authority hierarchy, disposal powers, and accountability matrix.
- District and state-level review committees should conduct periodic pendency and SLA compliance reviews.
- Every grievance must have a named accountable officer and supervisory reviewer.
- Monthly disposal quality audits should include speaking-order quality, evidence completeness, and citizen communication quality.

## 10) Functional Requirements

## 10.1 Complaint Registration

Mandatory fields:

- Complainant name (or anonymous token if policy allows)
- Contact (mobile/email/postal)
- Source channel
- Category/subcategory
- Description
- Location/jurisdiction
- Consent and privacy declaration

Optional:

- Attachments (images, PDFs, audio)
- Preferred language
- Vulnerability marker (senior citizen, disability, etc.)

## 10.2 Grievance Management

- Create grievance from single complaint or grouped complaints.
- Link/unlink complaints with audit reasons.
- Auto-prioritization by severity and affected population.
- Bulk communication to linked complainants.

## 10.3 Case Actions

- Notes, tasks, reminders, visit scheduling.
- Evidence upload and versioning.
- Internal comments vs citizen-visible comments.
- Reopen and appeals workflow.

## 10.4 Communication

- Multilingual templates.
- Event-based notifications:
  - Registered
  - Assigned
  - Under investigation
  - Resolved
  - Closed
  - Reopened/Appeal update

## 10.5 Government-Specific Functional Controls

- **Jurisdiction Validation**: Map each complaint to administrative geography (village/ward/block/district/state).
- **Citizen Charter Mapping**: Auto-tag grievances with service standard and target disposal timeline.
- **Speaking Order Template**: Standard closure format with facts, action taken, rule basis, and escalation option.
- **Mandatory Hearing Log (if required)**: Capture date, participants, notes, and outcome.
- **Office Memorandum Tracking**: Link grievance actions to circulars/notifications/department instructions.

## 11) Non-Functional Requirements

- **Availability**: 99.5%+ preferred for production.
- **Performance**: Complaint registration under 2 seconds (P95) for standard payloads.
- **Scalability**: Horizontal scale for intake spikes.
- **Security**: Encryption in transit and at rest; RBAC/ABAC.
- **Privacy**: Data minimization, consent tracking, masking of sensitive fields.
- **Auditability**: Immutable event trail for all status and ownership changes.
- **Interoperability**: APIs for CRM, ERP, call centre, and reporting tools.
- **Accessibility**: WCAG-friendly UI, voice-assisted intake for call centre.

### 11.1 Technical Quality Targets (Recommended)

- **RPO/RTO**: RPO <= 15 minutes, RTO <= 4 hours for critical GRM services.
- **Observability**: Centralized logs, metrics, and distributed traces with 90-day searchable retention.
- **Reliability SLOs**: API success rate >= 99.0% (monthly) for complaint registration and grievance updates.
- **Security Monitoring**: Privileged actions and data-export events must generate real-time audit alerts.
- **Data Integrity**: All state transitions must be append-only in audit logs with actor, timestamp, and reason.

## 12) Monitoring, Analytics, and KPIs

Minimum dashboard metrics:

- Total complaints vs total grievances.
- Consolidation ratio (complaints per grievance).
- SLA compliance by priority and department.
- Average resolution time (ART).
- First response time (FRT).
- Escalation rate and reopened rate.
- Repeat grievance rate (same category/location).
- Citizen satisfaction (CSAT) post closure.

Public-sector oversight metrics (recommended):

- Pendency aging buckets (0-7, 8-15, 16-30, >30 days).
- Disposal quality score (reopen rate + appeal uphold rate).
- Department-wise and district-wise SLA compliance heatmap.
- Essential service grievance resolution rate.
- Appeals volume and appellate disposal turnaround time.

Recommended reports:

- Weekly operations report.
- Monthly governance committee report.
- Root-cause and preventive action report.

## 13) Compliance and Control Checklist

- Role-based access matrix approved.
- Data retention and deletion policy defined.
- Consent and privacy notices published.
- Field audit and evidence standards documented.
- Appeals and committee TOR (terms of reference) published.
- Vulnerable complainant handling SOP available.
- Business continuity and disaster recovery tested.

Government compliance controls (as applicable):

- Record retention and archival aligned with applicable public records rules.
- Proactive disclosure/readiness for audit and oversight bodies.
- Statutory timeline compliance evidence maintained for appealable categories.
- Tamper-evident audit logs for disposal decisions and officer actions.

### 13.1 Audit Evidence Pack (Submission Ready)

- Approved SOP and policy documents with version and approver details.
- SLA breach and escalation reports for at least one representative review period.
- Sample end-to-end case timelines showing complaint registration to closure.
- Access-control evidence (role matrix, user provisioning/deprovisioning logs).
- Backup and restore test record with outcome and sign-off.
- Security incident log and closure record (if applicable).

## 14) Standard Operating Procedure (SOP) Summary

1. Register complaint within SLA.
2. Validate and classify.
3. Group into existing grievance where applicable.
4. Assign accountable owner.
5. Investigate with evidence.
6. Resolve with corrective action.
7. Communicate outcome to all linked complainants.
8. Close with proof and feedback capture.
9. Reopen/appeal when justified.
10. Track systemic actions for recurrence prevention.

## 15) Implementation Roadmap

### Phase 1 (MVP)

- Multi-channel intake: web, mobile, call centre, hardcopy entry.
- Complaint registration, acknowledgement, and status tracking.
- Basic grievance grouping and SLA timers.

### Phase 2

- Advanced deduplication (NLP similarity), dynamic routing, escalation automation.
- Governance dashboards and committee workflows.

### Phase 3

- Predictive analytics for hotspot detection.
- Automated root-cause classification and preventive recommendations.

## 16) Risks and Mitigations

| Risk                                         | Impact                     | Mitigation                                      |
| -------------------------------------------- | -------------------------- | ----------------------------------------------- |
| Poor data quality from offline/call channels | Wrong routing/grouping     | Mandatory validation + supervisor sampling      |
| Over-grouping unrelated complaints           | Citizen dissatisfaction    | Confidence threshold + triage review queue      |
| Under-grouping duplicates                    | Operational overload       | Improved matching model + analyst feedback loop |
| SLA breaches due to ownership ambiguity      | Escalations and trust loss | RACI matrix + auto-assignment rules             |
| Inadequate communication                     | Repeat complaints          | Template-driven lifecycle notifications         |

## 17) Acceptance Criteria

The GRM is considered ready when:

- Complaints from all defined channels are ingested in a common schema.
- Multiple complaints can be reliably linked to one grievance.
- SLA timers and escalation workflows run automatically.
- Full case audit trail is available.
- Dashboards expose grievance lifecycle and performance metrics.
- Reopen/appeal process is active and measurable.

## 18) Appendix — Example Status Lifecycle

`NEW -> ACKNOWLEDGED -> TRIAGED -> ASSIGNED -> IN_PROGRESS -> RESOLVED -> CLOSED`

Alternate branch:
`CLOSED -> REOPEN_REQUESTED -> REOPENED -> IN_PROGRESS -> RESOLVED -> CLOSED`

## 19) Recommended Minimum Policy Documents

- Grievance Redressal Policy
- SLA and Escalation Policy
- Data Privacy and Retention Policy
- Appeals and Review Committee Charter
- Vulnerable Complainant Protection SOP
- Citizen Charter service-level matrix
- Department grievance delegation order and authority matrix
- Disposal speaking-order template and review SOP

## 20) Final Submission Readiness Checklist

- All intake channels tested with successful complaint registration and acknowledgement.
- Complaint-to-grievance grouping validated for high, medium, and no-match scenarios.
- SLA timers and multi-level escalations tested with evidence.
- Reopen/appeal path tested with complete status traceability.
- Dashboard KPIs verified against source records.
- Audit trail export validated for sample cases.
- Policy, SOP, and committee governance records attached for submission.

## 21) Final Note

This architecture ensures that **every complaint is recorded**, **related complaints are consolidated into manageable grievances**, and **resolution is measurable, transparent, and accountable** across all channels.

## 22) Architecture and Flow Diagrams

Detailed architecture and process diagrams are available in:

- `GRIEVANCE_REDRESSAL_ARCHITECTURE_AND_FLOWS.md`
