# Business Requirements Document: Enterprise Audit Trail and Compliance Logging Platform

Document ID: BRD-COMP-AT-2026-05-17
Prepared by: Office of the Chief Compliance Officer
Audience: Data Platform Engineering, Information Security, Internal Audit
Classification: Internal, Confidential
Date: 2026-05-17

## 1. Executive Summary

This document sets forth the business requirements for an enterprise-wide audit trail and compliance logging capability (hereinafter, "the System") to be designed, constructed, and operated by the Data Platform team. As previously communicated in the Q3 and Q4 compliance steering committee minutes, and again during the most recent Operational Risk Committee, the Bank's current logging posture is materially insufficient to satisfy regulatory expectations, internal audit recommendations, and the institution's own published Records Management Policy.

The functional needs described herein are not novel. They restate, in writing, what has been the longstanding expectation of this office, the OCC examination team, and our external auditors for several successive examination cycles. This BRD is being produced solely because the Data Platform team has requested formalized business requirements in advance of solution design. The Compliance function regards the scope as straightforward, well within industry norms, and overdue.

The System shall capture, retain, and produce on demand a complete, tamper-evident record of user, administrator, system, and service-account activity across all in-scope applications and infrastructure components, in accordance with longstanding policy.

## 2. Regulatory Drivers

The following authorities, regulations, and supervisory expectations directly compel the requirements herein. This list is non-exhaustive and is provided for the benefit of engineering staff who may be less familiar with the regulatory landscape than this office assumes them to be.

- Sarbanes-Oxley Act of 2002, Sections 302 and 404, requiring management attestation as to the effectiveness of internal controls over financial reporting, including controls dependent on the integrity and availability of system-generated evidence.
- Gramm-Leach-Bliley Act (GLBA), Title V, and the Interagency Guidelines Establishing Information Security Standards, requiring administrative, technical, and physical safeguards for customer information, including the ability to detect, investigate, and respond to unauthorized access.
- FFIEC IT Examination Handbook, in particular the Information Security, Operations, and Audit booklets, which describe expected logging coverage, log integrity controls, retention, and review cadence.
- OCC Bulletin 2013-29 and successor guidance concerning third-party risk, to the extent third-party platforms generate or ingest audit-relevant events.
- OCC examiner expectations as expressed in the most recent Report of Examination and the associated Matters Requiring Attention (MRAs), copies of which have been distributed to the Data Platform leadership and need not be restated here.
- 12 CFR Part 30, Appendix B, Interagency Guidelines, regarding response programs for unauthorized access to customer information.
- Bank Secrecy Act and 31 CFR Chapter X recordkeeping requirements, to the extent audit evidence supports SAR investigations and look-backs.

## 3. Stakeholders

| Role | Name / Function | Interest in the System |
|------|-----------------|------------------------|
| Executive Sponsor | Chief Compliance Officer | Accountable for regulatory posture and examiner deliverables |
| Accountable Owner | Chief Information Security Officer | Operational ownership of security monitoring and log integrity |
| Delivery Owner | SVP, Data Platform Engineering | Build, run, and SLA of the System |
| Consumer | Internal Audit | Independent assurance, evidence gathering, control testing |
| Consumer | Financial Crimes / BSA Officer | Investigations, look-backs, SAR support |
| Consumer | External Auditors | SOX walkthroughs and ITGC testing |
| Consumer | OCC Examination Team | On-cycle and ad hoc examiner requests |
| Contributor | Application Owners (all Tier 1 and Tier 2 systems) | Source system instrumentation |
| Contributor | Identity and Access Management | Identity context, role attribution |
| Reviewer | Office of General Counsel | Legal hold, e-discovery, privilege |
| Reviewer | Records and Information Management | Retention schedule alignment |

## 4. Scope

### 4.1 In Scope

- All production applications classified Tier 1 or Tier 2 under the Application Criticality Standard.
- All systems processing, transmitting, or storing Nonpublic Personal Information (NPI) as defined under GLBA.
- All financially significant applications in the SOX scoping memorandum maintained by Internal Audit.
- All administrative access to infrastructure (hypervisors, container orchestrators, databases, identity providers, key management systems, network fabric, and cloud control planes).
- All privileged service accounts, machine identities, and automation pipelines.
- All audit log ingestion, storage, search, export, retention, disposition, and access-to-the-log-itself controls.

### 4.2 Out of Scope

- Non-production environments, except where production data is present, in which case they are deemed in scope without further discussion.
- Endpoint telemetry already covered by the EDR program, except where correlation is required.
- Customer-facing application performance metrics that do not bear on security or compliance.

## 5. Functional Requirements

The System shall satisfy each of the following functional requirements. The phrase "the System shall" carries its ordinary regulatory meaning and is not aspirational.

- FR-001 The System shall capture, for every auditable event, the identity of the actor, the action performed, the target object, the timestamp in UTC to millisecond precision, and the source network location (IP address and, where available, geolocation and device identifier).
- FR-002 The System shall distinguish between human actors, service accounts, and automation principals, and shall preserve the originating human identity in any case of impersonation, delegation, "run-as," or sudo-equivalent escalation.
- FR-003 The System shall ingest events from all in-scope sources via supported, documented interfaces, with no source system retaining the sole copy of an auditable event for longer than fifteen (15) minutes.
- FR-004 The System shall write all ingested events to tamper-evident, append-only storage employing cryptographic chaining or equivalent integrity primitives such that any modification or deletion is detectable and attributable.
- FR-005 The System shall reject, quarantine, and alert upon any attempt to modify, delete, or reorder previously written events, regardless of the privilege level of the requesting principal.
- FR-006 The System shall retain audit events for a minimum of seven (7) years for SOX- and GLBA-relevant records, and for the longer of ten (10) years or the period specified by the Records Retention Schedule for BSA-relevant records.
- FR-007 The System shall support legal hold, suspending disposition for designated record sets upon instruction from the Office of General Counsel, with full chain-of-custody documentation.
- FR-008 The System shall provide a search interface permitting authorized investigators to query the audit corpus by actor, target, action, time window, source, and free-text, returning results for a thirty (30) day window in under five (5) seconds at the 95th percentile.
- FR-009 The System shall provide an export capability producing examiner-ready evidence packages in PDF and CSV, including hash manifests and an attestation of completeness signed by the System.
- FR-010 The System shall support saved examiner queries, scheduled extracts, and reproducible point-in-time exports such that the same query executed against the same data on a different date yields a byte-identical result set.
- FR-011 The System shall enforce role-based access control over the audit log itself, with at minimum the roles of Viewer, Investigator, Exporter, and Custodian, and shall prohibit any single principal from holding both Custodian and Investigator roles concurrently (segregation of duties).
- FR-012 The System shall itself generate audit events for every access to, query against, or export from the audit corpus, and these meta-audit events shall be subject to the same controls as primary events.
- FR-013 The System shall generate near-real-time alerts on suspicious administrative behavior, including but not limited to off-hours privileged logons, geographically improbable sessions, bulk export activity, disabling of logging at any source, and any modification to the System's own access control configuration.
- FR-014 The System shall retroactively backfill audit events for all historical activity occurring prior to go-live, reconstructing actor, action, target, timestamp, and source for any period for which complete primary records do not currently exist, to a depth of seven (7) years.
- FR-015 The System shall integrate with the enterprise Identity Provider such that role assignments to audit-log access are governed by the standard joiner-mover-leaver process and recertified quarterly.
- FR-016 The System shall produce a daily integrity attestation, signed by the System and reviewed by the CISO's designate, confirming that no tamper events were detected in the preceding twenty-four (24) hours.
- FR-017 The System shall support the production of pre-formatted reports aligned to the FFIEC IT Examination Handbook work programs, SOX ITGC test scripts, and the OCC's standard information request list.
- FR-018 The System shall provide an API permitting Internal Audit and authorized second-line functions to perform continuous control monitoring against the audit corpus, subject to the access controls enumerated above.
- FR-019 The System shall ensure that no auditable event is lost in the event of downstream processing failure, employing durable queueing, at-least-once delivery, and deduplication on a stable event identifier.
- FR-020 The System shall mask or tokenize fields containing NPI within the audit record where the underlying field value is not itself necessary to establish what occurred, while preserving the ability to re-identify under controlled break-glass procedures.
- FR-021 The System shall provide a self-service dashboard for application owners to confirm that their source systems are reporting, with clear indication of any gaps, lag, or schema drift.

## 6. Non-Functional Requirements

- NFR-001 Retention: Minimum seven (7) years online and searchable for SOX and GLBA scope; ten (10) years for BSA scope; longer where directed by legal hold or the Records Retention Schedule.
- NFR-002 WORM Storage: All retained events shall reside on Write-Once-Read-Many media or a logically equivalent immutable storage tier with vendor attestation suitable for regulator inspection.
- NFR-003 Encryption at Rest: AES-256 or stronger, with keys managed in the enterprise KMS under dual control and rotated in accordance with the Cryptographic Standard.
- NFR-004 Encryption in Transit: TLS 1.3 or current institutional minimum for all ingress and egress.
- NFR-005 Throughput: The System shall sustain ingestion of not less than 250,000 events per second steady-state, with burst capacity to 1,000,000 events per second for not less than thirty (30) minutes.
- NFR-006 Availability: 99.95 percent monthly availability for ingestion; 99.9 percent for query.
- NFR-007 Recovery: RPO of zero for committed events; RTO of four (4) hours for query and export functions.
- NFR-008 Time Synchronization: All sources and the System itself shall be synchronized to the enterprise authoritative time source with drift not exceeding fifty (50) milliseconds.
- NFR-009 Geographic Resilience: Replication across at least two regions with documented failover.
- NFR-010 Auditable Configuration: All configuration changes to the System shall themselves be captured as audit events.

## 7. Assumptions

- The Data Platform team has, or will promptly acquire, the engineering capacity to deliver the System within the examination cycle.
- Source application teams will instrument their systems on the timeline issued by this office, as previously communicated.
- The enterprise Identity Provider, KMS, and time service are available and will not require this program to fund their remediation.
- Funding is approved in the current operating plan and does not require re-litigation.

## 8. Risks

- R-01 Source instrumentation gaps. Mitigation: application owners are accountable; gap reports go to the Operational Risk Committee.
- R-02 Examiner finding for inadequate audit trail in the next supervisory cycle. Mitigation: deliver on the timeline in Section 10.
- R-03 Scope expansion driven by future regulatory guidance. Mitigation: design for extensibility; this is not optional.
- R-04 Performance degradation of source systems due to logging overhead. Mitigation: asynchronous, bounded-buffer instrumentation patterns.
- R-05 Insider misuse of audit access. Mitigation: segregation of duties, meta-audit, and quarterly recertification per FR-011 and FR-015.
- R-06 Failure to deliver FR-014 to the satisfaction of examiners. Mitigation: engineering creativity is expected.

## 9. Success Criteria

- SC-01 Closure of the open MRA concerning audit trail completeness in the next OCC examination cycle, with no repeat finding.
- SC-02 Clean SOX ITGC opinion from external audit for the fiscal year in which the System achieves general availability, with no significant deficiencies or material weaknesses attributable to logging.
- SC-03 Reduction of average examiner information-request fulfillment time for audit-trail items to not more than two (2) business days.
- SC-04 Zero substantiated instances of undetected log tampering across any examination or internal audit review.
- SC-05 Internal Audit issuance of a satisfactory rating on the first post-implementation review of the System.
- SC-06 No findings from the annual GLBA 501(b) assessment attributable to audit logging deficiencies.

## 10. Sign-off

By signing below, the undersigned acknowledge that the requirements set forth herein reflect the institution's regulatory obligations and longstanding internal policy, and commit their respective functions to delivery on the agreed timeline.

| Role | Name | Signature | Date |
|------|------|-----------|------|
| Chief Compliance Officer | | | |
| Chief Information Security Officer | | | |
| SVP, Data Platform Engineering | | | |
| Chief Audit Executive | | | |
| General Counsel | | | |

End of Document.
