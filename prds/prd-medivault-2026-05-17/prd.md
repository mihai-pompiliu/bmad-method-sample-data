# Product Requirements Document: MediVault Patient Records Exchange

---

## Document Control

| Field | Value |
|---|---|
| Document Title | MediVault Patient Records Exchange — Product Requirements Document |
| Document ID | PRD-MVPRE-2026-001 |
| Version | 1.4 (Approval Candidate) |
| Status | Approval Candidate — Pending CISO and Chief Medical Information Officer countersignature |
| Classification | Confidential — Restricted Distribution (Tier 2). Contains references to protected health information (PHI) handling architecture. Distribution limited to named reviewers and approvers. |
| Effective Date | 2026-05-17 |
| Next Scheduled Review | 2026-08-17 (quarterly cadence per internal SDLC policy SEC-DOC-007) |
| Authors | J. Reyes (Senior Product Manager, Records Exchange), Dr. M. Ngo (Clinical Informaticist, MD MPH), L. Pillai (Compliance Lead, CHPC CIPP/US) |
| Contributing Reviewers | Office of the General Counsel; Information Security Office (CISO delegate); Privacy Officer; Director of Health Information Management (HIM); Director of Identity & Access Management; Director of Quality and Patient Safety; External counsel (Wilson Sonsini, healthcare privacy practice — advisory only). |
| Approvers (Required Signatures) | CIO, CISO, Chief Privacy Officer, Chief Medical Information Officer, VP Product, VP Engineering |
| Related Documents | ARCH-MVPRE-2026-001 (Solution Architecture), SEC-MVPRE-2026-001 (Threat Model and STRIDE Worksheet), DPIA-MVPRE-2026-001 (Data Protection Impact Assessment), BCP-MVPRE-2026-001 (Business Continuity Plan), TVA-MVPRE-2026-001 (Third-Party Vendor Assessment Index) |
| Change Control | All material changes require Change Advisory Board (CAB) approval per ITIL-aligned change management policy CHG-007. Editorial corrections may be applied by document owner with version increment. |

### Revision History

| Version | Date | Author | Summary of Changes |
|---|---|---|---|
| 0.1 | 2026-02-04 | J. Reyes | Initial outline, scope draft. |
| 0.4 | 2026-02-26 | J. Reyes, Dr. Ngo | Added clinical workflows, persona drafts, regulatory background. |
| 0.7 | 2026-03-19 | L. Pillai | Compliance matrix, risk register first pass, residency requirements. |
| 1.0 | 2026-04-08 | All | First internal review draft circulated to CAB. |
| 1.2 | 2026-04-30 | J. Reyes | Incorporated CAB feedback: tightened FR-12 through FR-18, added FHIR error handling, broke out audit retention NFRs. |
| 1.3 | 2026-05-12 | L. Pillai, J. Reyes | Added state-level law section (CA CMIA, NY SHIELD), reworked consent revocation flow, clarified break-the-glass provisions. |
| 1.4 | 2026-05-17 | J. Reyes | Approval candidate. Final non-goals list, traceability stub, open issues triaged. |

---

## 1. Executive Summary

MediVault Patient Records Exchange (hereafter "MediVault" or "the Platform") is a HIPAA-compliant, multi-tenant patient health records exchange platform that enables patients to grant time-limited, scope-limited, revocable access to their medical records — including laboratory results, diagnostic imaging, clinical encounter notes, and prescription history — to credentialed care providers operating across distinct health systems. The Platform is patient-mediated by design: every disclosure is anchored to an affirmative, auditable patient consent event, and every access by a provider is logged with sufficient fidelity to satisfy 45 CFR §164.528 accounting-of-disclosures requirements and to support both internal and Office for Civil Rights (OCR) audit inquiries.

MediVault is not an electronic health record (EHR) system, a health information exchange (HIE) in the regional/state-designated sense, a clearinghouse, or a payer-facing claims pipeline. It is, narrowly and intentionally, a consent-mediated records access fabric layered on top of the FHIR R4 APIs that EHR vendors are required to expose under the 21st Century Cures Act and the Office of the National Coordinator (ONC) Cures Act Final Rule. MediVault aggregates records from EHR-side FHIR endpoints (Epic, Cerner/Oracle Health, MEDITECH, Allscripts/Veradigm, athenahealth, and other certified Health IT modules), normalizes them against the United States Core Data for Interoperability (USCDI) v3 dataset, and exposes them to authorized providers via a SMART on FHIR–compatible interface gated by patient consent.

The strategic rationale for MediVault is threefold. First, the regulatory environment has shifted. The ONC information-blocking rules at 45 CFR Part 171 impose civil monetary penalties on actors who unreasonably interfere with electronic health information (EHI) access, exchange, or use. Health systems are operationally exposed: they must produce records on patient request, and they must not erect non-permitted barriers to inter-system exchange. MediVault provides a defensible, auditable path to compliance. Second, the clinical case is compelling. Care fragmentation across health systems is a documented patient safety risk; duplicated imaging, contraindicated prescriptions, and missed allergies are well-characterized harms. Patient-mediated exchange materially reduces these harms when adoption thresholds are met. Third, the patient-experience case is increasingly forced by market dynamics: patients expect to control their health data with the same granularity they exercise over financial and identity data.

This document specifies the product scope, regulatory framing, functional and non-functional requirements, integration surface, risk register, compliance control mapping, phased delivery plan, acceptance criteria, and success metrics for MediVault. It is the controlling product specification for the program; deviations require change-control approval. The document is deliberately verbose where regulatory citation precision matters and deliberately controlled in vocabulary where ambiguity would create compliance or clinical-safety exposure.

The Platform's commercial model is tenant-based: each provider organization (a covered entity or, where appropriate, a business associate operating under a Business Associate Agreement) onboards as an isolated tenant. Tenant isolation is enforced cryptographically and at the data-plane query level. MediVault itself operates as a business associate to each tenant, with executed BAAs in place prior to any PHI flowing through the system, and with all downstream subprocessors flowed-down under BAA chain.

The intended primary outcome of MediVault v1, against which success will be measured, is the elimination of fax-, courier-, and CD-ROM-mediated records exchange for participating tenants across the patient-mediated use case, replaced by a consented, audited, sub-second-retrieval digital exchange.

---

## 2. Background and Regulatory Context

### 2.1 HIPAA Privacy Rule (45 CFR Part 160 and Part 164, Subparts A and E)

The HIPAA Privacy Rule governs the use and disclosure of protected health information by covered entities and their business associates. MediVault is architected on the premise that every record movement is either (a) a disclosure pursuant to a valid patient authorization under §164.508, or (b) a permitted use or disclosure under §164.506 (treatment, payment, healthcare operations — limited to treatment in our v1 scope). The Platform does not rely on §164.512 public-interest disclosures, does not perform marketing or sale of PHI, and does not engage in §164.514 de-identification or limited-data-set workflows in v1.

The minimum necessary standard at §164.502(b) is operationalized via scope-limited consent (see FR-10 through FR-18). A consent grant in MediVault is never an unscoped grant; it always specifies record categories, date ranges, and time-to-live (TTL). The Platform refuses to issue grants without these parameters.

### 2.2 HIPAA Security Rule (45 CFR §§164.302–164.318)

The Security Rule imposes administrative, physical, and technical safeguards. MediVault's compliance posture maps to the standards and addressable specifications at §164.308 (Administrative Safeguards), §164.310 (Physical Safeguards — inherited from cloud infrastructure provider attestations), §164.312 (Technical Safeguards — access control, audit controls, integrity, person or entity authentication, transmission security), §164.314 (Organizational Requirements), and §164.316 (Policies and Procedures and Documentation Requirements). A detailed control-to-feature mapping appears in §11 of this document.

### 2.3 HITECH Act (Public Law 111-5, Title XIII)

The HITECH Act of 2009 strengthened HIPAA enforcement, established the Breach Notification Rule at 45 CFR §§164.400–414, extended direct liability to business associates, and accelerated the move to electronic exchange. MediVault's breach-detection posture (see FR-32 through FR-35) is calibrated to support the §164.404 individual notification deadlines (60 days from discovery), the §164.406 media notification thresholds (500 or more individuals in a state or jurisdiction), and the §164.408 HHS notification timelines.

### 2.4 21st Century Cures Act and ONC Cures Final Rule

The 21st Century Cures Act (Public Law 114-255) and the subsequent ONC Cures Act Final Rule (45 CFR Part 170 and Part 171) establish: (a) mandatory FHIR R4 API access via certified Health IT, (b) the United States Core Data for Interoperability (USCDI) dataset specification, and (c) the information-blocking prohibition. MediVault is built to consume the FHIR R4 APIs that all certified EHR modules must expose by the regulatory deadlines, and to provide patients and authorized providers a non-discriminatory, non-blocking access experience consistent with §171.103. Information-blocking exceptions (the eight permitted exceptions, e.g., Preventing Harm, Privacy, Security, Infeasibility, Health IT Performance) are documented in operational runbooks and surfaced as structured deny-with-reason responses rather than silent failures.

### 2.5 ONC Trusted Exchange Framework and Common Agreement (TEFCA)

MediVault v1 is not a Qualified Health Information Network (QHIN) and does not seek QHIN designation in this release. The Platform is designed, however, to be TEFCA-compatible: data structures, identity verification, and audit posture are aligned to the Common Agreement obligations such that a future QHIN-participant pathway is not architecturally foreclosed. This is a forward-compatibility commitment, not a v1 functional requirement.

### 2.6 State Law Variations

HIPAA establishes a federal floor. State laws that are more stringent preempt HIPAA in those respects. MediVault must accommodate, at minimum:

- **California Confidentiality of Medical Information Act (CMIA)** — Civil Code §§56–56.37. Imposes stricter authorization standards, including written or electronic-signature requirements for certain disclosures, and provides a private right of action with statutory damages. MediVault consent UX in California-resident workflows includes CMIA-conformant authorization language and signature capture.
- **New York SHIELD Act** — General Business Law §899-bb. Imposes reasonable safeguards for private information; aligned to our baseline security posture.
- **Texas Medical Records Privacy Act (HB 300)** — Health & Safety Code Chapter 181. Imposes training and consumer-access requirements broader than HIPAA.
- **42 CFR Part 2 (Substance Use Disorder Records)** — Not in v1 scope; SUD records are excluded by category filter (see Non-Goals §15).
- **State-specific genetic information laws** — Variable; MediVault flags genetic test results as a controlled record category requiring explicit grant.

### 2.7 Adjacent Regulatory Surface (Out of Scope for v1, In Scope for Awareness)

- GDPR (EU General Data Protection Regulation) — Not in v1 scope; no EU deployment. See Non-Goals.
- PIPEDA (Canada) — Not in v1 scope.
- FDA Software as a Medical Device (SaMD) — MediVault does not perform clinical decision support, does not interpret data, and is not classified as SaMD. This non-classification is a deliberate design constraint; see §15 Non-Goals.

---

## 3. Stakeholders

### 3.1 Patients

The data subject and consent grantor. Patients hold the authority to grant, scope, time-bound, and revoke access to their records. Patients are not tenants; they hold accounts that are tenant-agnostic and portable. Patient experience requirements (accessibility, plain-language consent, mobile-first) are first-class. See personas §4.1–4.2.

### 3.2 Care Providers (Clinical End Users)

Licensed clinicians (physicians, advanced practice providers, nurses, pharmacists where in scope) who request and view records to inform care decisions. Providers operate within a tenant context and are credentialed by their employing organization. Providers are not the consent authority; they are consent consumers.

### 3.3 Compliance and Privacy Officers

The internal compliance owners at each tenant organization. They are responsible for monitoring access patterns, responding to patient inquiries about disclosures, responding to OCR investigations, and producing accounting-of-disclosures reports under §164.528. MediVault must surface a complete, queryable, exportable view of all tenant-scoped activity.

### 3.4 Health System IT and Identity Administrators

Tenant administrators who manage user provisioning, deprovisioning, role assignment, and SSO/IdP configuration. They are responsible for the timeliness and accuracy of clinician role membership, which directly drives access permissions. They are also accountable for clinician termination workflows that revoke access within the timelines established in §164.308(a)(3)(ii)(C).

### 3.5 External Auditors

Third-party auditors engaged by tenants for SOC 2, HITRUST, internal audit, or OCR-prompted investigations. External auditors require time-bounded, read-only, scoped access to audit data — and that access itself is auditable.

### 3.6 Office for Civil Rights (OCR) and State Regulators

Government enforcement bodies. MediVault does not provide direct OCR access; it provides tenant compliance officers with the export tooling needed to respond to OCR inquiries within statutory timeframes (typically 30–60 days from request).

### 3.7 EHR Vendor Partners

Epic, Oracle Health (Cerner), MEDITECH, Allscripts/Veradigm, athenahealth, and other certified Health IT developers whose FHIR APIs MediVault consumes. Relationship is technical-integration only; no commercial dependency in v1.

### 3.8 Cloud Infrastructure Provider

The Platform runs on a HIPAA-eligible cloud (AWS, in our reference architecture), under a signed BAA. The cloud provider is a subprocessor to MediVault, which is itself a business associate to tenants.

### 3.9 Internal Engineering, Product, and Customer Success

The product-building and tenant-supporting functions. They operate under defined least-privilege roles; no engineer has standing access to PHI. Just-in-time access workflows with break-the-glass logging are specified in §6.

---

## 4. User Personas

### 4.1 Persona: Maria — Chronically Ill Patient, Multi-System Care

- **Age / Context:** 58, type 2 diabetic with cardiac comorbidities, sees specialists across three unaffiliated health systems and an independent endocrinology practice.
- **Goals:** Avoid repeating diagnostic tests; ensure each specialist sees current medication list and recent labs; control which providers see her behavioral-health-adjacent records.
- **Frustrations:** Has historically printed PDFs, mailed CD-ROMs of imaging, hand-carried lab results. Has experienced contraindicated prescription events that a unified record would have prevented.
- **Technical Comfort:** Moderate. Uses a smartphone confidently, uses online banking, but has not used patient portals beyond basic appointment booking.
- **Regulatory Awareness:** Low-to-moderate. Knows "HIPAA" as a general concept; does not understand the boundaries.
- **Implication for MediVault:** Consent UX must be plain-language, default-private, scope-explicit, with clear revocation paths. Onboarding must not require regulatory literacy.

### 4.2 Persona: Marcus — Young Adult, Episodic Care

- **Age / Context:** 27, generally healthy, occasional urgent-care visits, recently relocated cross-country.
- **Goals:** Quickly grant a new PCP access to existing records without coordinating a fax from his prior PCP.
- **Frustrations:** Records-request bureaucracy; delays of 10–30 days for records transfer.
- **Technical Comfort:** High. Mobile-native, expects bank-grade authentication including biometric and hardware-key options.
- **Regulatory Awareness:** Low. Defers to platform defaults.
- **Implication for MediVault:** Onboarding-to-first-grant time must be measured in minutes, not days. Biometric/passkey authentication must be a first-class option.

### 4.3 Persona: Dr. Patel — Emergency Department Physician

- **Age / Context:** 42, ED attending at a large urban academic medical center.
- **Goals:** Obtain rapid access to records for incoming patients, particularly medication lists, allergies, recent imaging, and cardiac history. Sub-minute access targets.
- **Frustrations:** Patients arrive without history; current HIE options are slow, inconsistent in coverage, and produce non-normalized data.
- **Technical Comfort:** High clinical workflow comfort; impatient with non-clinical software friction.
- **Regulatory Awareness:** Moderate. Knows that ED workflows have specific permitted-disclosure latitudes but is not expert in §164.510 limitations.
- **Implication for MediVault:** Break-the-glass workflow (FR-19) must be discoverable, fast, and produce robust justification capture for retrospective audit. Information density at the record view is critical.

### 4.4 Persona: Dr. Lee — Primary Care Physician, Small Independent Practice

- **Age / Context:** 51, PCP in a four-provider independent practice on a smaller EHR vendor.
- **Goals:** Receive pre-visit records from referring specialists and prior PCPs; avoid manual data entry.
- **Frustrations:** Records arrive after the visit, in unsearchable PDFs.
- **Technical Comfort:** Moderate. Practice has limited IT support.
- **Regulatory Awareness:** Moderate-to-high. Has personally responded to OCR letters.
- **Implication for MediVault:** Provider portal must work without deep IT lift. SSO must support smaller IdPs and a strong username/password+MFA fallback.

### 4.5 Persona: R. Chen — Compliance Officer, Regional Health System

- **Age / Context:** 46, CHPC-credentialed, oversees privacy and security across 14 hospitals and 80 ambulatory sites.
- **Goals:** Produce defensible accounting-of-disclosures reports; detect anomalous access patterns; respond to OCR inquiries within mandated timelines.
- **Frustrations:** Existing audit tools are siloed per system and require manual reconciliation. Patient inquiries about "who saw my record" take weeks to answer.
- **Technical Comfort:** Moderate-to-high. Comfortable with reporting tools, SQL-adjacent.
- **Regulatory Awareness:** Expert.
- **Implication for MediVault:** Compliance console must produce 6-year-lookback access reports under §164.528 and 7-year audit-log retention reports under HITRUST guidance, with exportable evidence packages.

### 4.6 Persona: S. Okafor — External Auditor

- **Age / Context:** 38, IT audit manager at a Big Four firm conducting tenant SOC 2 Type II engagements.
- **Goals:** Sample access events, verify control effectiveness, produce auditor-ready evidence.
- **Frustrations:** Inconsistent log formats across audited clients.
- **Technical Comfort:** High.
- **Regulatory Awareness:** High.
- **Implication for MediVault:** Standardized auditor role with time-boxed credentials, tamper-evident log export, hash-chain verifiability.

---

## 5. User Journeys

### 5.1 Journey: Patient Grants Access During an Emergency Department Visit

1. Patient arrives at ED, presents identification, is registered.
2. Triage nurse identifies that patient has out-of-system care history.
3. Patient is offered an in-app or kiosk MediVault consent flow.
4. Patient authenticates via existing MediVault account (passkey, biometric, or SMS OTP fallback), or completes expedited identity proofing at IAL2 if first-time user.
5. Patient is presented a scoped grant: "Grant View Memorial Hospital ED access to: Medications, Allergies, Recent Imaging (last 90 days), Recent Labs (last 30 days), Encounter Notes (last 12 months). Duration: 24 hours. Revocable at any time."
6. Patient affirms grant. Consent record is hash-chained, timestamped, signed.
7. ED clinical staff sees the patient's records appear in their MediVault provider portal (or via EHR-side SMART app launch).
8. Every access by ED staff is logged with clinician identity, role, timestamp, record category, and access reason.
9. At 24-hour TTL expiration, access is automatically revoked. Patient receives notification.

### 5.2 Journey: Provider Requests Records Pre-Appointment

1. PCP office schedules new-patient appointment 7 days out.
2. PCP office staff initiates a records request via MediVault provider portal, identifying patient by name+DOB+verification code provided by patient.
3. MediVault sends patient a request notification via app push, SMS, and email.
4. Patient reviews scoped request: requesting org, requesting clinician, requested categories, duration, stated purpose ("New patient intake — prior records review").
5. Patient can approve in full, modify scope (e.g., decline mental health records), or deny.
6. Upon approval, records are pre-loaded; provider can review prior to appointment.
7. Grant auto-expires 7 days after first appointment date.

### 5.3 Journey: Compliance Officer Runs a 12-Month Access Review

1. Compliance officer logs into MediVault Compliance Console under MFA + step-up authentication.
2. Initiates query: "All accesses to records of patients in tenant T-447, calendar year 2025, by clinician role 'Resident', where access reason ≠ direct treatment relationship."
3. System returns paginated, filterable result set with per-event drill-down.
4. Compliance officer flags 12 events for follow-up; each flag generates a workflow ticket assigned to relevant department head.
5. Officer exports an evidence package: CSV + signed JSON + hash manifest, all encrypted under officer-provided public key.
6. The query itself, and the export, are themselves audit events.

### 5.4 Journey: Patient Revokes Accidental Grant

1. Patient realizes (within MediVault app activity feed) that they granted broader access than intended to an out-of-network provider.
2. Patient taps "Revoke" on the active grant.
3. System immediately invalidates access tokens, terminates any in-flight sessions on the provider side, and posts a revocation event to the audit log.
4. Patient is shown a complete list of records that were viewed by that provider prior to revocation, with timestamps.
5. Patient can optionally file an inquiry to the provider's compliance office through MediVault; inquiry is routed and tracked.
6. Provider compliance office receives the inquiry as a structured ticket with the underlying audit excerpt attached.

---

## 6. Functional Requirements

Requirements are numbered FR-NN and grouped by capability area. Each requirement has a priority (MUST / SHOULD / MAY in RFC 2119 sense) and a v1 / v1.x / future designation.

### 6.1 Authentication, Identity, and Authorization

- **FR-1 (MUST, v1)** — Patient identity verification at account creation MUST meet NIST SP 800-63-3 IAL2 for first-time users granting any record access. Acceptable evidence types: government ID + selfie liveness, or in-person proofing at participating provider sites.
- **FR-2 (MUST, v1)** — Patient authentication MUST support AAL2 minimum, with AAL3 step-up for sensitive actions (consent grant modification, account recovery). Supported authenticators: FIDO2 passkeys (preferred), platform biometric, TOTP authenticator app, SMS OTP (with documented risk acceptance per SP 800-63B §5.1.3.3 considerations).
- **FR-3 (MUST, v1)** — Provider authentication MUST integrate with tenant IdP via SAML 2.0 or OIDC. Direct-credential fallback MUST require MFA.
- **FR-4 (MUST, v1)** — Provider authorization MUST be role-based and scoped to tenant context. Cross-tenant access by a clinician requires re-authentication in the other tenant context.
- **FR-5 (MUST, v1)** — Session timeouts: provider sessions idle-expire at 15 minutes; patient sessions idle-expire at 10 minutes for sensitive views, 60 minutes for read-only summary views.
- **FR-6 (MUST, v1)** — Account recovery flows MUST NOT permit recovery via knowledge-based authentication alone (e.g., security questions). Recovery requires at least one in-person, video-verified, or strong-evidence path.
- **FR-7 (SHOULD, v1)** — Patients SHOULD be able to designate a verified proxy (caregiver, parent of minor, healthcare power of attorney) with documented chain-of-authority. Proxy grants are themselves auditable as distinct from patient-direct grants.
- **FR-8 (MUST, v1)** — Failed authentication attempts MUST be rate-limited per account and per source IP, with adaptive escalation. Successful authentications and authentication failures MUST be logged with sufficient detail for forensic reconstruction.
- **FR-9 (MUST, v1)** — Clinician termination workflows triggered by tenant IdP MUST revoke all active MediVault sessions and tokens within 60 seconds of receipt of the termination signal.

### 6.2 Consent and Grant Management

- **FR-10 (MUST, v1)** — A grant MUST specify: grantor (patient), grantee (named provider organization and, optionally, named individual clinician), record category set (selected from a controlled vocabulary mapped to USCDI v3), date range of records covered, grant TTL (start and end timestamp), and stated purpose.
- **FR-11 (MUST, v1)** — Grants MUST default to the narrowest reasonable scope; the system MUST NOT pre-select "all records, all time, indefinite duration" as a default option.
- **FR-12 (MUST, v1)** — Grants MUST be revocable by the patient at any time, with revocation taking effect within 60 seconds globally.
- **FR-13 (MUST, v1)** — Grant modification (scope expansion, TTL extension) MUST require re-authentication at AAL3 and MUST produce a new consent record; existing grants are not silently mutated.
- **FR-14 (MUST, v1)** — Consent records MUST be cryptographically signed by both the patient (via the authenticator-bound key) and the platform (via a platform signing key with documented key management).
- **FR-15 (MUST, v1)** — Consent UX MUST be presented in plain language at no higher than an 8th-grade reading level; localized at minimum to English, Spanish, Simplified Chinese, Vietnamese, Tagalog. Additional locales per market need.
- **FR-16 (SHOULD, v1)** — System SHOULD detect and warn on apparently over-broad grants (e.g., "all records, all time, indefinite") with a friction prompt requiring affirmation.
- **FR-17 (MUST, v1)** — California-resident patients MUST be presented CMIA-conformant authorization language and signature capture when granting access to records covered by CMIA.
- **FR-18 (MUST, v1)** — Patients MUST be able to view the complete history of their grants — active, expired, revoked — with full detail, indefinitely (or until account deletion).

### 6.3 Break-the-Glass (Emergency Access)

- **FR-19 (MUST, v1)** — A break-the-glass workflow MUST exist for credentialed providers in defined emergency contexts (ED, code situations). Access requires: (a) clinician affirmation of emergency, (b) structured reason capture, (c) supervisor or compliance notification within the same workday, (d) automatic retrospective audit flag, (e) post-event patient notification within 7 days unless prohibited under §164.510(b)(3) considerations.
- **FR-20 (MUST, v1)** — Break-the-glass invocations MUST be reported on a compliance-officer dashboard with daily review SLA.

### 6.4 Records Ingestion (EHR-Side FHIR Integration)

- **FR-21 (MUST, v1)** — MediVault MUST consume FHIR R4 resources from each supported EHR vendor, conforming to US Core Implementation Guide v6.1.0 or later. Required resource types include, at minimum: Patient, Practitioner, Encounter, Condition, MedicationRequest, MedicationStatement, AllergyIntolerance, Immunization, Observation (lab and vital subsets), DiagnosticReport, DocumentReference (clinical notes), Procedure, CarePlan, Goal, ImagingStudy.
- **FR-22 (MUST, v1)** — Ingestion MUST use SMART on FHIR Backend Services (system-level OAuth 2.0 client credentials with JWT assertion) where supported, with patient-context launches where appropriate.
- **FR-23 (MUST, v1)** — Ingestion errors (404, 410, malformed bundles, schema drift) MUST be classified, logged, and surfaced to operations dashboards. Silent ingestion failure is prohibited.
- **FR-24 (MUST, v1)** — FHIR endpoint deprecation by an EHR vendor MUST trigger a documented migration plan with timelines, surfaced to affected tenants no less than 60 days in advance where possible.
- **FR-25 (SHOULD, v1)** — Bulk Data Access (FHIR Bulk Data IG / `$export`) SHOULD be used for initial tenant onboarding and periodic reconciliation where the EHR endpoint supports it.
- **FR-26 (MUST, v1)** — Ingested records MUST be normalized to a canonical internal representation that preserves source fidelity (the original FHIR resource is retained and signed) while enabling cross-source querying.

### 6.5 Records Access (Provider-Side)

- **FR-27 (MUST, v1)** — Authorized providers MUST be able to retrieve granted records via (a) the MediVault provider web portal, (b) a SMART on FHIR–launched in-EHR view (where the tenant EHR supports a SMART app), and (c) an authenticated FHIR R4 API for programmatic access by integrated systems.
- **FR-28 (MUST, v1)** — Every record access MUST be authorization-checked against the current grant state at the time of access; cached authorization decisions older than 60 seconds MUST NOT be relied upon for sensitive categories.
- **FR-29 (MUST, v1)** — Record views MUST include provenance metadata: source EHR, source tenant (if applicable), original record creation timestamp, ingestion timestamp.
- **FR-30 (MUST, v1)** — Record export to external formats (PDF, CDA) MUST itself be a logged event and MUST require step-up authentication.
- **FR-31 (SHOULD, v1)** — A provider-side "no records found within grant scope" response MUST be distinguishable from "grant denied" and from "system unavailable" to support information-blocking exception transparency.

### 6.6 Audit and Compliance

- **FR-32 (MUST, v1)** — Every event of consequence (authentication, grant creation/modification/revocation, record access, record export, configuration change, administrative action, key rotation, ingestion success/failure) MUST be written to an append-only, hash-chained audit log within 5 seconds of occurrence.
- **FR-33 (MUST, v1)** — Audit log entries MUST include: actor identity, actor role, tenant context, timestamp (RFC 3339 with millisecond precision, UTC), event type, target resource (where applicable), outcome, source IP, user-agent, correlation ID.
- **FR-34 (MUST, v1)** — Audit logs MUST be tamper-evident (Merkle-tree hash-chained, periodic Merkle root publication to an out-of-band store), MUST be retained for 7 years, and MUST be exportable on-demand by authorized compliance officers.
- **FR-35 (MUST, v1)** — Access-specific logs supporting §164.528 accounting of disclosures MUST be retained for 6 years and MUST be queryable by patient identifier and by date range with sub-30-second response time on a single-patient lookback query.
- **FR-36 (MUST, v1)** — The Compliance Console MUST support queries by tenant, by clinician, by patient, by record category, by access reason, by time window, and by anomaly classification.
- **FR-37 (SHOULD, v1)** — Anomaly detection SHOULD flag: unusual after-hours access, access to high-profile patient records (VIP list maintained by tenant), bulk access exceeding tenant-defined thresholds, geographic anomalies.

### 6.7 Patient Self-Service

- **FR-38 (MUST, v1)** — Patients MUST have a self-service portal (web + iOS + Android) supporting: account creation, identity verification, viewing their own records, granting access, revoking access, viewing access history, downloading their own records, requesting amendments per §164.526.
- **FR-39 (MUST, v1)** — Patients MUST be able to download a portable copy of all their records in standard formats (FHIR JSON bundle, CDA, PDF summary) at no cost, satisfying §164.524 right-of-access.
- **FR-40 (MUST, v1)** — Patients MUST be able to file an amendment request under §164.526; the request is routed to the source-of-record provider's compliance office with structured payload and tracked to resolution.
- **FR-41 (MUST, v1)** — Patients MUST be able to obtain an accounting of disclosures covering the prior 6 years (per §164.528) on demand.
- **FR-42 (SHOULD, v1)** — Patients SHOULD receive timely notifications (push, email, SMS configurable) for: new grant created, grant accessed for the first time, grant near expiration, grant expired, grant revoked, anomalous activity.

### 6.8 Provider Portal

- **FR-43 (MUST, v1)** — Provider portal MUST present granted records grouped by patient with clear scope and TTL indicators.
- **FR-44 (MUST, v1)** — Provider portal MUST display the current grant terms to the clinician at the point of access; a clinician cannot avoid seeing the scope and stated purpose.
- **FR-45 (MUST, v1)** — Provider portal MUST support clinician note-back (a structured response visible to subsequent grant recipients in the patient's record exchange context, where the patient has consented to such note-backs).
- **FR-46 (SHOULD, v1)** — Provider portal SHOULD provide a longitudinal view across multiple historical grants from the same patient where the patient has authorized continuity.

### 6.9 Admin and Tenant Management

- **FR-47 (MUST, v1)** — Tenant onboarding MUST require: executed BAA, organizational identity verification, designated security and privacy contacts, IdP integration, baseline role catalog.
- **FR-48 (MUST, v1)** — Tenant administrators MUST be able to manage their own users (provision, role-assign, deprovision) without MediVault staff involvement.
- **FR-49 (MUST, v1)** — Tenant data MUST be logically and cryptographically isolated; cross-tenant data egress paths MUST NOT exist outside of patient-mediated consent flows.
- **FR-50 (MUST, v1)** — Tenant offboarding MUST produce a verified data-export package and a verifiable data-destruction certificate within contractually agreed timeframes.

---

## 7. Non-Functional Requirements

Presented in matrix form for traceability. Each NFR carries a measurable target and a verification method.

| NFR ID | Category | Requirement | Target | Verification |
|---|---|---|---|---|
| NFR-1 | Availability | Platform availability (control plane + record retrieval) | 99.95% monthly, excluding scheduled maintenance | Synthetic probes + status page; monthly SLO review |
| NFR-2 | Availability | Consent grant/revoke critical path | 99.99% monthly | Independent probe suite |
| NFR-3 | Latency | Record retrieval p99 (cached path) | < 800 ms | Production telemetry |
| NFR-4 | Latency | Record retrieval p99 (cold ingestion path) | < 3.5 s | Production telemetry |
| NFR-5 | Latency | Grant revocation propagation | < 60 s globally | Synthetic probe |
| NFR-6 | Latency | Audit event durability commit | < 5 s from occurrence | Internal test harness |
| NFR-7 | Data Residency | All PHI stored within US regions | 100% | Cloud-account level guardrails (SCPs / equivalent), audited monthly |
| NFR-8 | Data Residency | No cross-border replication of PHI | 100% | Architecture review; quarterly attestation |
| NFR-9 | Encryption | Encryption at rest | AES-256 (FIPS 140-2/3 validated modules) | KMS configuration audit |
| NFR-10 | Encryption | Encryption in transit | TLS 1.3 minimum; TLS 1.2 deprecated by end of v1.1 | Continuous scan |
| NFR-11 | Encryption | Tenant key isolation | Per-tenant data encryption key, envelope-encrypted under tenant KMS CMK | Cryptographic review |
| NFR-12 | Encryption | Customer-managed key (CMK) support for enterprise tenants | Supported via BYOK via AWS KMS | Documentation + integration test |
| NFR-13 | Logging | Audit log retention | 7 years minimum | Storage policy + retention audit |
| NFR-14 | Logging | Access log retention (for §164.528) | 6 years minimum, indexed | Storage policy + retention audit |
| NFR-15 | Logging | Log integrity | Hash-chained, periodic Merkle root anchoring | Log integrity verification job |
| NFR-16 | Compliance | SOC 2 Type II | Achieved by end of Phase 3 | External audit |
| NFR-17 | Compliance | HITRUST CSF Certification (r2) | Achieved by end of Phase 4 | External assessor |
| NFR-18 | Compliance | HIPAA Security Rule conformance | Continuous | Internal control assessment + control mapping in §11 |
| NFR-19 | Compliance | BAAs with all subprocessors | 100% prior to PHI flow | Vendor management system |
| NFR-20 | Security Testing | External penetration test cadence | At minimum annually; additionally after material architectural change | Third-party engagement |
| NFR-21 | Security Testing | Continuous automated security testing | SAST + DAST + SCA + IaC scanning in CI | Pipeline configuration |
| NFR-22 | Security Testing | Bug bounty program | Active by Phase 2 | Program launch |
| NFR-23 | Backup / DR | Recovery Time Objective (RTO) | 4 hours for control plane; 1 hour for read path | DR exercise |
| NFR-24 | Backup / DR | Recovery Point Objective (RPO) | 15 minutes | DR exercise |
| NFR-25 | Backup / DR | DR exercise cadence | Semi-annual | Calendared |
| NFR-26 | Accessibility | WCAG 2.1 Level AA conformance for patient and provider UIs | 100% | VPAT + accessibility audit |
| NFR-27 | Internationalization | Locale support at v1 | en-US, es-US, zh-Hans, vi, tl | Translation QA |
| NFR-28 | Performance | Audit query (single-patient, 12-month lookback) | < 30 s | Production benchmark |
| NFR-29 | Performance | Bulk export (one tenant, one year) | < 4 hours | Production benchmark |
| NFR-30 | Scalability | Concurrent active grants | 50M | Load testing |
| NFR-31 | Scalability | Daily record-access events | 100M | Load testing |
| NFR-32 | Observability | Mean time to detect (MTTD) for production incidents affecting PHI confidentiality | < 15 minutes | Incident postmortems |
| NFR-33 | Observability | Mean time to respond (MTTR) — initial response | < 30 minutes | Incident postmortems |
| NFR-34 | Privacy | DPIA on file and reviewed | Annually and on material change | DPIA register |
| NFR-35 | Privacy | Privacy notice and consent UX legal review | Per release | OGC sign-off |
| NFR-36 | Operations | Standing engineering access to production PHI | Zero standing access; JIT only with break-glass logging | IAM audit |
| NFR-37 | Operations | Change management | All production changes via CAB-approved pipelines | CAB records |
| NFR-38 | Operations | Vulnerability management — critical CVEs | Patched within 7 days | Vulnerability tracker |
| NFR-39 | Operations | Vulnerability management — high CVEs | Patched within 30 days | Vulnerability tracker |
| NFR-40 | Operations | Phishing-resistant authenticators required for privileged internal roles | 100% | IAM audit |

---

## 8. Integration Requirements

### 8.1 FHIR R4 Endpoints (Inbound from EHRs)

MediVault consumes FHIR R4 endpoints exposed by certified Health IT modules. Conformance baseline is US Core IG v6.1.0 or later. Required interactions: `read`, `search`, `$everything` (where supported and licensed), Bulk Data `$export`. Authentication via SMART on FHIR (system-level and patient-context).

Supported vendor endpoints at v1 GA:

- Epic (via Epic on FHIR developer program; production endpoint per tenant)
- Oracle Health / Cerner (Code Console; FHIR R4 endpoints)
- Allscripts / Veradigm (FHIR R4 program)
- athenahealth (athenahealth Developer Portal)
- MEDITECH (Greenfield FHIR APIs)
- eClinicalWorks (FHIR APIs)
- NextGen Healthcare (FHIR APIs)

Endpoint discovery follows the SMART .well-known/smart-configuration pattern. Tenant-specific endpoint configuration is captured at onboarding.

### 8.2 FHIR R4 Endpoints (Outbound to Provider Systems)

MediVault exposes a FHIR R4 server (subset of resources) for authenticated, authorized retrieval by integrated provider systems. SMART on FHIR app launch is supported.

### 8.3 SMART on FHIR Launch Contexts

- EHR launch (`launch` scope) — Provider opens MediVault as an embedded view from within their EHR.
- Standalone launch — Provider logs into MediVault directly.
- Patient-context launches — Patient launches MediVault from their EHR patient portal.

Scopes required at minimum: `openid`, `fhirUser`, `launch`, `patient/*.read` (restricted by grant), `user/*.read` (provider, restricted by tenant role and grant intersection).

### 8.4 IHE Profiles

For interoperability with legacy and HIE-adjacent systems, MediVault supports the following IHE profiles (compatibility-grade, not certification-grade in v1):

- IHE ITI-65 (Provide Document Bundle) — Inbound, for legacy submissions.
- IHE ITI-67 (Find Document References) — Outbound.
- IHE ITI-68 (Retrieve Document) — Outbound.
- IHE ATNA (Audit Trail and Node Authentication) — Audit log export format compatibility.

### 8.5 HL7 v2 (Legacy)

HL7 v2 messaging is not a v1 ingestion path. A v1.1 adapter accepts ADT^A01/A04/A08 messages for patient demographic synchronization with tenants whose source EHRs do not yet expose Patient FHIR resources reliably. Clinical content (lab, results, notes) is not ingested via v2 in v1 or v1.1.

### 8.6 Identity Providers

SAML 2.0 and OIDC. Tested against: Okta, Microsoft Entra ID (Azure AD), Ping Identity, Duo SSO, Auth0, and Active Directory Federation Services. SCIM 2.0 supported for user provisioning.

### 8.7 Secure Messaging and Direct Project

Direct Secure Messaging (Direct Project) is not in v1 scope. Compliance with Direct Trust framework deferred to v1.2 evaluation.

---

## 9. Risk Register

| Risk ID | Description | Likelihood | Impact | Inherent Risk | Mitigations | Residual Risk | Owner |
|---|---|---|---|---|---|---|---|
| R-1 | Phishing-based credential compromise of clinician account leading to unauthorized PHI access | High | High | Critical | Phishing-resistant MFA for privileged roles; FIDO2 passkey rollout; user awareness training; anomaly detection; rapid revocation tooling | Medium | CISO |
| R-2 | Sub-tenant data leakage (cross-tenant access) | Medium | Critical | High | Per-tenant encryption keys; query-layer tenant predicate enforcement; automated tenant-isolation test suite in CI; quarterly red-team isolation tests | Low | VP Engineering |
| R-3 | FHIR endpoint deprecation or breaking change by EHR vendor | High | Medium | High | Vendor change-monitoring; multi-vendor abstraction layer; 60-day customer comms; staged migration runbooks | Medium | Director Integrations |
| R-4 | OCR audit finding triggered by tenant complaint or breach | Low | Critical | High | §164.528 compliant accounting; documented control mapping; tabletop exercises; incident response runbooks aligned to §164.404–414 | Medium | Chief Privacy Officer |
| R-5 | Ransomware affecting platform availability or integrity | Medium | Critical | High | Immutable backups; segregated key custody; ransomware-resistant restore drills; EDR; least-privilege; phishing-resistant MFA | Medium | CISO |
| R-6 | Insider threat — credentialed staff misuse | Low | High | Medium | Zero standing access; JIT access with break-glass logging; behavioral analytics; mandatory leave for privileged roles | Low | CISO |
| R-7 | Patient identity proofing fraud at onboarding | Medium | High | High | IAL2 enforcement; liveness detection; document forensic vendor; cross-check against verified provider sources where available | Medium | Director IAM |
| R-8 | Information-blocking complaint against MediVault or a tenant operating through MediVault | Medium | High | High | Information-blocking exception logging; transparent deny-with-reason responses; OGC pre-clearance of policy defaults | Medium | OGC + CPO |
| R-9 | Subprocessor BAA gap or supply-chain compromise | Low | Critical | High | Vendor management program; flow-down BAA enforcement; SOC 2 review at onboarding and annually; subprocessor change notification | Low | Vendor Risk Mgmt |
| R-10 | Key management failure (loss or compromise of platform signing key or tenant KEK) | Low | Critical | High | HSM-backed keys; documented key ceremony; quarantine and rotation runbooks; quorum-based recovery | Low | CISO |
| R-11 | Cross-region disaster affecting primary AWS region | Low | High | Medium | Multi-region replication architecture; documented failover; semi-annual DR exercise | Low | VP Engineering |
| R-12 | Audit log tampering or loss | Low | Critical | High | Append-only storage; hash-chaining; Merkle root anchoring to independent store; segregated log-admin role | Low | CISO |
| R-13 | Patient consent UX defects yielding "uninformed consent" claims | Medium | High | High | Plain-language review; legal sign-off per release; usability testing with target literacy levels; localization QA | Medium | Product + OGC |
| R-14 | Mistaken release of 42 CFR Part 2 records (SUD) into a non-Part-2 disclosure path | Low | Critical | High | Categorical exclusion in v1; ingestion-side filter on flagged SUD records; refusal-with-reason if SUD records are present | Low | CPO |
| R-15 | Public-figure or VIP patient inappropriate access | Medium | High | High | Tenant-maintained VIP lists with elevated monitoring; mandatory break-glass-equivalent logging for VIP access; weekly review SLA | Medium | Tenant Compliance |

---

## 10. Compliance Matrix

Mapping selected HIPAA Security Rule controls (§164.308 Administrative Safeguards, §164.310 Physical Safeguards, §164.312 Technical Safeguards) to MediVault features and requirements. This is not an exhaustive control mapping; the full control catalog is maintained in the Compliance Control Register (CCR-MVPRE-2026-001).

| Control | Title | Implementation in MediVault | Linked Requirements |
|---|---|---|---|
| §164.308(a)(1)(i) | Security Management Process | Documented security program, risk analysis on file, sanction policy | NFR-18, NFR-34 |
| §164.308(a)(1)(ii)(A) | Risk Analysis | Annual formal risk analysis; this PRD's §9 is an input | §9 risk register |
| §164.308(a)(1)(ii)(B) | Risk Management | Risk treatment plan with owners and review cadence | §9 |
| §164.308(a)(1)(ii)(C) | Sanction Policy | HR-integrated sanction process; audit-driven referrals | N/A (HR policy) |
| §164.308(a)(1)(ii)(D) | Information System Activity Review | Compliance Console; anomaly detection | FR-36, FR-37 |
| §164.308(a)(3)(i) | Workforce Security | Role-based access; provisioning workflows; termination revocation | FR-3, FR-4, FR-9 |
| §164.308(a)(3)(ii)(C) | Termination Procedures | Sub-60-second revocation on IdP termination signal | FR-9 |
| §164.308(a)(4)(i) | Information Access Management | Tenant-scoped RBAC; consent-mediated patient data access | FR-4, FR-10–FR-18 |
| §164.308(a)(5)(ii)(D) | Password Management | AAL2/AAL3 authenticators; phishing-resistant for privileged | FR-2, FR-3, NFR-40 |
| §164.308(a)(6)(ii) | Response and Reporting | Incident response runbooks aligned to breach notification | NFR-32, NFR-33 |
| §164.308(a)(7)(i) | Contingency Plan | BCP/DR documented and exercised | NFR-23–NFR-25 |
| §164.308(a)(7)(ii)(A) | Data Backup Plan | Immutable backups; documented restore | NFR-23–NFR-25 |
| §164.308(a)(7)(ii)(B) | Disaster Recovery Plan | Multi-region; semi-annual exercise | NFR-23–NFR-25 |
| §164.308(a)(7)(ii)(C) | Emergency Mode Operation | Break-the-glass workflow; degraded-mode procedures | FR-19, FR-20 |
| §164.308(a)(8) | Evaluation | Internal control assessment annually | NFR-18 |
| §164.308(b)(1) | Business Associate Contracts | BAAs with all subprocessors; flow-down requirements | NFR-19 |
| §164.310(a)(1) | Facility Access Controls | Inherited from cloud provider attestations | Cloud BAA |
| §164.310(d)(1) | Device and Media Controls | No on-premises media; cloud provider attestations | Cloud BAA |
| §164.312(a)(1) | Access Control | Tenant + role + consent + grant intersection | FR-4, FR-10, FR-27, FR-28 |
| §164.312(a)(2)(i) | Unique User Identification | Per-user identity; no shared accounts | FR-3, FR-48 |
| §164.312(a)(2)(ii) | Emergency Access Procedure | Break-the-glass | FR-19 |
| §164.312(a)(2)(iii) | Automatic Logoff | Session timeout policies | FR-5 |
| §164.312(a)(2)(iv) | Encryption and Decryption | AES-256 at rest, TLS 1.3 in transit | NFR-9, NFR-10 |
| §164.312(b) | Audit Controls | Append-only, hash-chained audit logs | FR-32–FR-35, NFR-13–NFR-15 |
| §164.312(c)(1) | Integrity Controls | Hash-chain; record provenance signatures | NFR-15 |
| §164.312(d) | Person or Entity Authentication | IAL2/AAL2/AAL3 | FR-1, FR-2 |
| §164.312(e)(1) | Transmission Security | TLS 1.3; mutual TLS for system-to-system | NFR-10 |
| §164.316(a) | Policies and Procedures | Documented and version-controlled | Document control header |
| §164.316(b)(1) | Documentation | 6-year retention of policies and procedures | Retention policy |
| §164.316(b)(2)(i) | Time Limit | 6-year retention | Retention policy |
| §164.316(b)(2)(ii) | Availability | Internal access to current documentation | Confluence + Document Mgmt |
| §164.316(b)(2)(iii) | Updates | Quarterly review cadence | Document control header |

---

## 11. Phased Rollout / Milestones

MediVault will be delivered across four phases over fourteen months from the v1.0 development kickoff. Each phase has explicit gating criteria; phase exit requires sign-off by VP Product, VP Engineering, CISO, and Chief Privacy Officer.

### Phase 1 — Foundations (Months 1–4)

- Multi-tenant infrastructure with per-tenant key isolation.
- Patient identity verification (IAL2) and authentication (AAL2/AAL3 step-up).
- Provider identity federation (SAML 2.0, OIDC, SCIM 2.0).
- Consent grant data model and patient self-service grant/revoke UX (web).
- FHIR R4 ingestion adapter for Epic and athenahealth.
- Append-only, hash-chained audit log.
- Internal compliance console (read-only, queryable).
- Penetration test #1; remediation closure prior to Phase 2 exit.
- Phase exit criteria: 3 design partner tenants, 1,000 patient accounts, zero P0/P1 security findings open.

### Phase 2 — Provider Workflows and Scale (Months 5–8)

- SMART on FHIR provider portal app (in-EHR launch).
- Break-the-glass workflow.
- Compliance Console v1 (export, anomaly flags, accounting-of-disclosures queries).
- FHIR ingestion adapters: Oracle Health/Cerner, Allscripts, MEDITECH.
- Mobile applications (iOS, Android) — patient.
- Bug bounty program launch.
- Phase exit criteria: 10 production tenants, 50,000 patient accounts, p99 retrieval latency target met, SOC 2 Type I report on file.

### Phase 3 — Compliance Hardening and Certification (Months 9–11)

- SOC 2 Type II audit period commences and completes.
- HITRUST CSF readiness assessment.
- Information-blocking exception logging and reporting.
- California CMIA-conformant consent flow.
- Localized UX (es-US, zh-Hans, vi, tl).
- Accessibility certification (WCAG 2.1 AA VPAT).
- Phase exit criteria: SOC 2 Type II report issued, HITRUST gap remediation plan locked, VPAT published.

### Phase 4 — Scale, Certify, Expand (Months 12–14)

- HITRUST CSF r2 certification achieved.
- Additional FHIR adapters: eClinicalWorks, NextGen.
- IHE ITI-65/67/68 compatibility.
- Bulk export performance targets met.
- Customer-managed key (BYOK) support GA.
- Phase exit criteria: HITRUST r2 certificate issued, 30 tenants, 500,000 patient accounts, retention of NPS targets (see §13).

---

## 12. Acceptance Criteria

Per major capability area. Acceptance is gated on automated test pass, manual exploratory review, security review, and (where applicable) regulatory review.

### 12.1 Identity and Authentication

- Patient identity proofing rejection rate due to identity fraud signals tracked and within calibrated band.
- 100% of privileged internal users on phishing-resistant authenticators.
- Termination revocation propagation verified < 60 seconds end-to-end in test harness.

### 12.2 Consent Management

- Grant creation, modification, revocation flows pass automated functional tests covering ≥ 200 scenarios, including localized variants.
- Consent record signatures verifiable offline by an auditor with platform public key.
- No grant can be created without explicit scope, category, and TTL.

### 12.3 Records Ingestion

- For each supported EHR adapter: 100% of USCDI v3 mandatory resource types ingestible in a reference test environment.
- Schema-drift detection raises within one ingestion cycle of detection.

### 12.4 Records Access

- Authorization check is performed on every record retrieval; verified by integration tests against revocation race conditions.
- Provenance metadata present on every record view.

### 12.5 Audit and Compliance

- Hash-chain verification job runs nightly and alerts on any discontinuity.
- §164.528 accounting query for any individual patient returns in < 30 s p95.
- Tamper-evident log export reproduces independently verifiable hash manifest.

### 12.6 Patient Self-Service

- Patient can complete account creation → first grant in < 8 minutes p50, < 15 minutes p95 (excluding identity proofing wait).
- Patient downloadable record bundle conforms to FHIR R4 + US Core.

### 12.7 Provider Portal

- SMART app launches successfully against each Phase 2 supported EHR in a sandboxed test environment.
- Provider sees current grant scope and stated purpose without dismissable banner.

### 12.8 Admin / Tenant Management

- Tenant onboarding (post-BAA) completes within 5 business days from kickoff in 80% of cases.
- Tenant offboarding produces verified destruction certificate.

---

## 13. Success Metrics

### 13.1 Adoption Metrics

- Provider organization adoption: 10 tenants by end of Phase 2; 30 tenants by end of Phase 4.
- Patient account growth: 50,000 by end of Phase 2; 500,000 by end of Phase 4.
- Active grants outstanding: 200,000 by end of Phase 4.
- Patient grant volume: 1.5M grants created cumulatively by end of Phase 4.
- Records access events: 100M cumulatively by end of Phase 4.

### 13.2 Quality and Reliability Metrics

- SLO attainment: 99.95% control plane / 99.99% consent critical path, monthly.
- p99 retrieval latency target: < 800 ms sustained.
- Audit query latency: < 30 s p95.

### 13.3 Compliance Metrics

- OCR inquiries handled within statutory window: 100%.
- SOC 2 Type II issued: 0 material weaknesses.
- HITRUST CSF r2 certified.
- Pen-test findings: 100% critical/high closed within 30 days of report acceptance.
- Audit pass rate (tenant-driven SOC 2 sub-service reviews): 100% no qualifications.

### 13.4 Support / Operational Metrics

- Support ticket volume: < 4.0 tickets per 1,000 active patient users per month (steady state).
- Support ticket volume (provider side): < 1.5 tickets per 1,000 provider sessions per month.
- Median time-to-first-response: < 2 business hours.
- Median time-to-resolution (non-incident): < 2 business days.

### 13.5 User Satisfaction Metrics

- Patient NPS: ≥ +40 by end of Phase 3; ≥ +50 by end of Phase 4.
- Provider NPS: ≥ +30 by end of Phase 3; ≥ +40 by end of Phase 4.
- Compliance officer satisfaction (CSAT, structured survey): ≥ 4.3/5.0.

---

## 14. Traceability Matrix (Stub)

Full traceability is maintained in the requirements management system (Jira + Xray; reference: project MVPRE). This stub illustrates the structure expected across all FRs.

| Requirement | Test Case ID(s) | Test Type | Phase | Owner |
|---|---|---|---|---|
| FR-1 | TC-IAL2-001 through TC-IAL2-024 | Manual + integration | 1 | QA-Identity |
| FR-2 | TC-AUTH-101 through TC-AUTH-188 | Automated + manual | 1 | QA-Identity |
| FR-9 | TC-TERM-001 through TC-TERM-014 | Integration + chaos | 1 | QA-Identity |
| FR-10 | TC-GRANT-001 through TC-GRANT-076 | Automated | 1 | QA-Consent |
| FR-12 | TC-REVOKE-001 through TC-REVOKE-040 | Automated + race | 1 | QA-Consent |
| FR-19 | TC-BTG-001 through TC-BTG-022 | Manual + audit-log review | 2 | QA-Compliance |
| FR-21 | TC-FHIR-EPIC-001..., TC-FHIR-CERN-001..., TC-FHIR-ATHE-001... | Vendor sandbox | 1–2 | QA-Integrations |
| FR-32 | TC-AUDIT-001 through TC-AUDIT-058 | Automated + cryptographic verification | 1 | QA-Compliance |
| FR-35 | TC-528-001 through TC-528-019 | Performance + functional | 2 | QA-Compliance |
| FR-39 | TC-RIGHTACCESS-001 through TC-RIGHTACCESS-026 | Functional | 1 | QA-Patient |
| NFR-3 | LT-PERF-RETR-001 | Load + perf | 1–2 | Perf Eng |
| NFR-9 | SEC-CRYPTO-001 | Cryptographic review | 1 | Sec Eng |
| NFR-15 | SEC-LOG-CHAIN-001 | Cryptographic verification | 1 | Sec Eng |
| NFR-16 | AUDIT-SOC2-001 | External audit | 3 | Compliance |
| NFR-17 | AUDIT-HITRUST-001 | External audit | 4 | Compliance |

(Additional rows continue in the requirements management system for every FR and NFR.)

---

## 15. Non-Goals

The following are explicitly out of scope for MediVault v1. Each non-goal is intentional, reviewed, and may be reconsidered in a future release but is not deliverable under this PRD without an explicit scope-change process.

- MediVault is **not an EHR**. It does not capture clinical documentation at the point of care, does not generate orders, does not produce clinical notes, does not maintain a longitudinal chart as the system of record.
- MediVault does **not handle claims, billing, eligibility, prior authorization, or any payer-facing financial transaction**. No X12 EDI (270/271, 837, 835, etc.) integration in v1.
- MediVault does **not perform OCR or ingestion of paper records** in v1. Paper-only records remain outside scope.
- MediVault is **not internationally deployed** in v1. No EU, UK, Canadian, Australian, or APAC deployments. GDPR/PIPEDA conformance not pursued.
- MediVault does **not handle 42 CFR Part 2 substance use disorder records** as a categorical carve-out in v1. SUD records, where flagged at ingestion, are excluded; refusal-with-reason is surfaced. A v1.x Part 2 handling design is anticipated.
- MediVault does **not handle mental health record carve-outs** beyond standard category-level controls in v1. State-specific mental health record exceptions (e.g., psychotherapy notes under §164.508(a)(2)) are excluded categorically; psychotherapy notes are not ingested.
- MediVault does **not perform clinical decision support** of any kind. No alerts, no risk scores, no diagnostic suggestions. This is a deliberate design choice to remain outside FDA SaMD classification.
- MediVault is **not a QHIN under TEFCA** in v1. Compatibility is preserved; designation is not pursued.
- MediVault does **not integrate with Direct Project / Direct Secure Messaging** in v1.
- MediVault does **not provide patient-to-patient sharing** (e.g., a patient sharing their record with a family member who is not a credentialed provider via the provider portal). Patient self-download and personal forwarding is supported via FR-39.
- MediVault does **not provide telehealth, video, scheduling, or messaging** features.
- MediVault does **not perform deidentification or limited-data-set production** in v1. §164.514 workflows are excluded.
- MediVault does **not sell PHI**, does **not market on the basis of PHI**, does **not perform research-purpose disclosures** in v1.
- MediVault does **not ingest genomic data beyond report-level genetic test results** flagged as a controlled category. Raw genomic data files (VCF, BAM, FASTQ) are excluded.
- MediVault does **not ingest wearable / consumer device data** (Apple HealthKit, Google Fit, Fitbit) in v1.

---

## 16. Open Issues

Items deliberately unresolved at v1.4 of this PRD, tracked for resolution before respective phase gates.

- **OI-1** — Final policy decision on whether minors aged 12–17 are issued direct patient accounts or operate exclusively through verified-proxy parental accounts. State law variance is material; legal review in progress. Target resolution: Phase 1 mid.
- **OI-2** — Whether to support consent grants to **named individual clinicians** in addition to provider organizations as the grantee unit. Clinical workflow benefits vs. revocation complexity tradeoff. Target resolution: Phase 1 exit.
- **OI-3** — Treatment of **emergency contact / next-of-kin** access patterns under §164.510(b). Currently excluded. Stakeholder pressure to add a tightly-scoped variant. Target resolution: Phase 2.
- **OI-4** — Whether to support **patient-initiated record correction** (amendment under §164.526) inline in the patient app, or strictly route to provider compliance offices. Currently the latter (FR-40). Target resolution: Phase 2.
- **OI-5** — Default **break-the-glass post-event patient notification timing** — current spec is 7 days, some clinical leaders advocate same-day; tradeoff against ED workflow disruption. Target resolution: Phase 2 mid.
- **OI-6** — Approach to **VIP / public-figure patients** beyond tenant-maintained lists — whether MediVault should provide cross-tenant elevated-monitoring tooling, given the cross-tenant implications. Target resolution: Phase 3.
- **OI-7** — Whether **read-only auditor accounts** issued to external auditors should be tenant-scoped or service-wide for Big Four audit teams covering multiple tenants. Target resolution: Phase 3.
- **OI-8** — Resolution on **CMK / BYOK** key custody model for the most stringent enterprise tenants — full external HSM, AWS KMS external key store, or platform-managed CMK. Target resolution: Phase 4 entry.
- **OI-9** — Approach to **research-tier de-identified analytics** (potential future tier; excluded from v1, but architecture should not foreclose). Target resolution: post-v1.
- **OI-10** — Final answer on whether **provider-side notes back into the exchange** (FR-45) require source-EHR write-back (out of scope for v1) or remain MediVault-only annotations visible to subsequent grant recipients. Target resolution: Phase 2 entry.

---

## 17. Glossary

- **AAL** — Authenticator Assurance Level (NIST SP 800-63B).
- **BAA** — Business Associate Agreement.
- **CMIA** — California Confidentiality of Medical Information Act.
- **CMK** — Customer-Managed Key.
- **DPIA** — Data Protection Impact Assessment.
- **EHI** — Electronic Health Information.
- **EHR** — Electronic Health Record.
- **FHIR** — Fast Healthcare Interoperability Resources (HL7 R4 in this document).
- **HIE** — Health Information Exchange.
- **HITRUST CSF** — HITRUST Common Security Framework.
- **IAL** — Identity Assurance Level (NIST SP 800-63A).
- **IdP** — Identity Provider.
- **JIT** — Just-In-Time (access).
- **OCR** — HHS Office for Civil Rights.
- **ONC** — Office of the National Coordinator for Health Information Technology.
- **PHI** — Protected Health Information.
- **QHIN** — Qualified Health Information Network (TEFCA designation).
- **RPO / RTO** — Recovery Point / Recovery Time Objective.
- **SaMD** — Software as a Medical Device.
- **SMART on FHIR** — Substitutable Medical Applications and Reusable Technologies launch framework.
- **SUD** — Substance Use Disorder (42 CFR Part 2).
- **TEFCA** — Trusted Exchange Framework and Common Agreement.
- **USCDI** — United States Core Data for Interoperability.

---

## 18. Approvals

| Role | Name | Signature | Date |
|---|---|---|---|
| Chief Information Officer | _________ | _________ | _________ |
| Chief Information Security Officer | _________ | _________ | _________ |
| Chief Privacy Officer | _________ | _________ | _________ |
| Chief Medical Information Officer | _________ | _________ | _________ |
| Vice President, Product | _________ | _________ | _________ |
| Vice President, Engineering | _________ | _________ | _________ |

End of document. PRD-MVPRE-2026-001 v1.4 — Approval Candidate.
