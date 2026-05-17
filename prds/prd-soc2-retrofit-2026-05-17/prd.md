# PRD: RouteFront SOC2 Type II Readiness and Attestation Program

## Document Control

- **Document type:** Compliance-retrofit PRD (cross-functional)
- **Status:** Approved (board approval 2026-05-02, exec sponsor sign-off 2026-05-09)
- **Authors:** Head of Engineering, Compliance Lead
- **Reviewers:** CISO, VP Engineering, General Counsel (Legal)
- **External advisors:** Schellman (auditor of record), TBD GRC platform vendor (Vanta or Drata, selection in flight)
- **Version:** 1.0
- **Target Type II report issuance:** Q4 2027 (covering observation period 2026-09-01 through 2027-08-31)

## Executive Summary

RouteFront is a 4-year-old B2B SaaS workflow platform. As of this writing we serve approximately 85 customers with roughly $22M ARR. Two strategic prospects have explicitly conditioned their purchase on a SOC2 Type II report: a state-level public-sector buyer and a Fortune 500 healthcare distributor. Combined ACV of those two deals exceeds the program's all-in cost by roughly 4x in year one alone, and historical pipeline data suggests another 6 to 9 deals per year in the existing funnel are silently gated on the same requirement.

The board approved this program on 2026-05-02. We engaged Schellman to perform a readiness assessment in March and April 2026. Their report identified 14 gaps against the Security trust services criterion and a smaller set against Availability. This PRD captures the engineering and operational work required to close those gaps, sustain the controls through the 12-month observation window, and emerge with an unqualified Type II opinion.

This is a compliance-retrofit, not a greenfield product effort. The dominant input is the regulatory baseline. The capabilities below are the engineering changes required to meet that baseline. We are deliberately conservative about scope. Nothing here is intended to expand the platform's feature surface.

Plainly stated: this is the kind of program every B2B SaaS at our stage eventually runs, the kind that no one is excited to staff, and the kind that quietly determines whether we get to play in the enterprise segment for the next three years. We are running it now because we can no longer afford not to.

## Scope Statement

### In Scope (SOC2 boundary)

- The RouteFront production tenant environment (AWS us-east-1 and us-west-2).
- The data-plane services: workflow runtime, document store, integration gateway, notification service.
- The control-plane services: tenant admin API, billing service, audit subsystem.
- Internal staff access paths into production (SSO, bastion, break-glass).
- Subprocessor relationships that handle customer data (current count: 11).
- Backups, disaster recovery, and the recovery-test process.

### Out of Scope

- The marketing site (routefront.com) and the public docs portal.
- Internal corporate IT systems that do not touch customer data (HR, finance ledger, marketing tools).
- The dev and staging AWS accounts, except where they share IAM or networking with production.
- Customer-managed integrations running in the customer's own cloud.

## Trust Services Criteria Coverage

- **Security (CC series):** Primary. Full coverage. Non-negotiable.
- **Availability (A series):** Secondary. Included because both gating prospects requested it and the incremental control cost is modest given existing SRE practice.
- **Confidentiality (C series):** Optional add-on. Recommended for v1.1 (post initial report) if customer demand persists. Not in this program's v1 scope.
- **Processing Integrity, Privacy:** Not in scope. No demand signal, and the control overhead is not justified at current ARR.

## Gap Analysis Summary

Schellman's readiness report (delivered 2026-04-22) identified 14 gaps. The eight capability-level gaps that drive engineering work are itemized below in Required Capabilities. The remaining six are policy and process artifacts (formal information security policy, data classification policy, change management policy formalization, code of conduct attestation, background check process documentation, and onboarding/offboarding checklist standardization). Those are owned by the Compliance Lead with HR and do not require eng build work.

Severity distribution from the readiness report: 3 gaps rated High (capabilities 1, 2, and 3 in the list below), 6 rated Medium, and 5 rated Low. Schellman's working position is that the High-rated gaps are remediable inside the four-month design phase, that the Medium-rated gaps are largely process or tooling, and that none of the gaps are structural blockers to attestation. We concur with that read.

A point worth being explicit about: the gap list is shorter than typical for a company of our age and ARR. That is partly because we adopted reasonable defaults early (KMS for primary stores, SSO for staff, infra-as-code for production change). It is also partly because the readiness assessment scope was tight. We do not expect the audit fieldwork itself to surface a materially different picture, but the program's risk register should treat that as a known unknown.

## Required Capabilities

These are framed as control implementations, not feature requests. Each maps to one or more trust services criteria.

1. **Centralized audit logging for all data-plane actions.** Today audit events are scattered across four services with inconsistent schemas, retention, and access. Consolidate to a single append-only audit log with a documented schema, 13-month hot retention, and tamper-evident storage. The auditor will sample from this log during fieldwork. Target sink: an S3 bucket with Object Lock in Compliance mode, fronted by a managed query layer. Maps to CC7.2, CC7.3.

2. **Least-privilege RBAC for internal staff.** Current state: the "engineer" role has standing read access to production data stores. Target state: no standing prod read access for engineering; just-in-time elevation through an approval workflow with a default 4-hour TTL and full session audit. The on-call engineer retains a pre-approved elevation path so that incident response is not gated on getting an approver out of bed. Maps to CC6.1, CC6.3.

3. **Encryption at rest for tenant data, including backups.** Primary RDS instances are already encrypted with KMS CMKs. The gap is in our snapshot pipeline (cross-region replicated snapshots are using the default AWS-managed key, and the S3 bucket housing logical backup exports is unencrypted at the object level). Close both. Bonus: rotate the primary CMK once the snapshot pipeline is rekeyed, which also addresses a low-rated finding about key rotation evidence. Maps to CC6.7.

4. **Quarterly access reviews for internal staff.** Currently ad-hoc, performed by the VP Eng about twice a year on inconsistent scope. Target: quarterly reviews covering all production access grants, with documented evidence, sign-off, and remediation tracking. Owned by Compliance, tooled by the GRC platform. Maps to CC6.2.

5. **Vulnerability management program with documented SLAs.** Currently informal. Snyk and AWS Inspector findings are triaged ad-hoc. Target: documented severity-to-SLA mapping (critical 7 days, high 30 days, medium 90 days), automated ticketing, monthly metrics review. Maps to CC7.1.

6. **Vendor risk management (TPRM) program.** Build a formal subprocessor inventory, a DPA chain on file for every subprocessor that handles customer data, and an annual review process keyed to each vendor's own SOC2 or equivalent report. Initial inventory scope: 11 subprocessors. Maps to CC9.2.

7. **Incident response runbook and tabletop exercises.** Today, response is competent but undocumented. Target: a versioned IR runbook covering detection, triage, containment, eradication, recovery, and post-incident review, plus two tabletop exercises per year with documented outcomes. Maps to CC7.4, CC7.5.

8. **Customer-facing trust center.** A public page (trust.routefront.com) presenting the SOC2 report (gated by NDA via Vanta or Drata's portal), our subprocessor list, security FAQs, and uptime metrics. This is table stakes for the sales motion that the program is intended to unlock. Maps to CC2.3 (communication of system commitments) but is also a sales artifact.

A note on sequencing: capabilities 1, 2, and 3 are the High-rated gaps and must land before 2026-09-01. Capabilities 4, 5, 6, and 7 must be designed and have at least one cycle of evidence on file before 2026-09-01, but they continue to operate (and accrue evidence) throughout the observation window. Capability 8 is a sales asset and is fastest-to-ship but is gated on having a Type I or Type II report to publish, which means it lights up no earlier than late 2026 (Type I) or Q4 2027 (Type II). The trust center page can go live in a pre-launch state earlier than that, presenting the in-progress status, our security overview, and the subprocessor list.

## Constraints

- **No material customer-facing breaking changes during the observation window.** No auth flow rewrites, no API contract breaks, no forced reauth events for customers. The observation window is fragile. A botched rollout triggers a control exception that we then have to explain in the report.
- **Observation window starts 2026-09-01.** This is deliberate. It gives us four months from program kickoff to land the design and operate of every control before the auditor's clock starts. A slip past 2026-09-01 pushes the report from Q4 2027 to Q1 2028, which loses one of the two anchor deals.
- **Budget cap of $480,000.** Covers: Schellman fees (readiness plus Type I plus Type II observation plus report), GRC platform license (Vanta or Drata, 12 months), one external pen-test, and one part-time GRC consultant for the first 6 months. Internal headcount cost is not counted against this cap but is tracked separately.
- **Must not delay the Wave-2 feature roadmap.** Wave-2 is the funded growth bet for FY27. Compliance work draws from eng capacity but cannot displace Wave-2 critical path. We have modeled the load against current team velocity and believe this is achievable, but it is the program's single largest execution risk.

## Non-Goals

We are stating these explicitly to prevent scope creep. Compliance programs accrete scope; this section is the immune system.

- **Not pursuing HIPAA.** The healthcare-adjacent customer accepted SOC2 in lieu of HIPAA for their v1 use case. HIPAA is a separate program if and when demand materializes.
- **Not pursuing FedRAMP.** The public-sector buyer is state-level and explicitly accepted SOC2. FedRAMP Moderate is a 24-month, 7-figure program we are not entertaining at current ARR.
- **Not pursuing ISO 27001 in parallel.** Deferred to year 2 if international demand emerges. The control overlap with SOC2 is high but not high enough to justify the dual-attestation overhead this year.
- **Not migrating cloud regions or providers.** Current AWS posture is acceptable to the auditor. Multi-cloud and region migration debates are out of scope.
- **Not rewriting the legacy customer-admin UI.** Known tech debt, separate roadmap. The UI is in scope of the audit boundary but its rewrite is not in scope of this program.
- **Not productizing a trust center as a feature our customers offer to their own customers.** Some customers have asked. It is a real opportunity. It is not v1 of this program.
- **Not pursuing CSA STAR, SOC for Cybersecurity, or any other adjacent attestation.** One report. One opinion. One year.

## Timeline

- **2026-05 to 2026-08 (Design phase, 4 months):** Implement the eight engineering capabilities. Stand up the GRC platform. Author and approve policies. Train staff.
- **2026-09-01:** Observation window begins. Controls must be operating as designed from this date.
- **2026-09 to 2027-08 (Operation phase, 12 months):** Sustain controls. Quarterly access reviews. Two tabletop exercises. Continuous evidence collection through the GRC platform. Monthly compliance steering review.
- **2027-09 to 2027-11 (Audit fieldwork):** Schellman performs testing and walkthroughs.
- **2027-Q4:** Type II report issued. Sales enablement and customer distribution.

Optional milestone: pursue a SOC2 Type I report covering a point in time around 2026-10-01. Cost is modest (~$25K incremental). Benefit is that it unblocks the two anchor deals roughly 12 months earlier on a "Type I now, Type II to follow" basis. Decision pending in June.

## Budget Detail

The $480K cap allocates roughly as follows. Numbers are estimates pending vendor selection.

- Schellman fees, all phases (readiness already partially incurred, Type I optional, Type II observation, fieldwork, report): $135K to $165K.
- GRC platform license, 12 months at enterprise tier: $55K to $75K.
- One external pen-test (network and application scope): $35K to $50K.
- Just-in-time access tooling (buy path, estimate): $40K to $90K annual.
- Part-time GRC consultant, 6 months at roughly 0.5 FTE: $90K.
- Contingency and tooling overage (SIEM tuning, audit log infra, training): $50K to $75K.

This is a tight envelope. We are not building margin for a second auditor opinion or a second pen-test. If a major finding requires a re-test, that comes out of contingency or out of FY27 eng opex.

## Success Criteria

- Unqualified Type II opinion issued by Schellman covering 2026-09-01 through 2027-08-31.
- Zero control exceptions of "Material Weakness" or "Significant Deficiency" severity in the final report. Up to two minor exceptions tolerable.
- Both anchor prospects close. Combined first-year ACV exceeds program cost.
- No Wave-2 roadmap milestone slips by more than 4 weeks attributable to this program.
- Trust center live and indexable by 2026-10-01.
- Sales cycle length for security-sensitive prospects (defined as those whose security questionnaire exceeds 75 questions) decreases by 25% in the four quarters following report issuance.

## Open Questions and Risks

- **GRC platform selection.** Vanta and Drata are both viable. Decision target: 2026-06-01. Selection criteria document in progress. Risk: a wrong call here costs three months of evidence-collection rework.
- **Pen-test vendor.** Two finalists. The auditor does not require a specific vendor but does require the report. Decision target: 2026-07-15.
- **Just-in-time access tooling for capability 2.** Build vs. buy. Buy candidates (Teleport, ConductorOne, StrongDM) range from $40K to $90K per year. Build estimate is roughly 6 engineer-weeks plus ongoing maintenance. Leaning buy.
- **Type I decision.** As noted in Timeline. Decision target: 2026-06-15.
- **Capacity risk.** The constraint that Wave-2 cannot slip is the program's largest unknown. Mitigation: a named compliance-eng squad (2 backend engineers, 1 SRE, 0.5 EM) carved from general bench, plus the part-time GRC consultant. We will re-baseline at the end of June.
- **Subprocessor churn.** If any of the 11 current subprocessors materially changes its own security posture during the observation window, we may inherit an exception. Mitigation: TPRM program (capability 6) is sequenced first.
- **Acquisition risk.** If the company is acquired during the observation window, the report's continuity is at the acquirer's discretion. Out of program control, noted for awareness.
- **Auditor continuity.** Our lead engagement partner at Schellman is expected to stay on through the Type II report. If they roll off mid-engagement, a new partner will require a re-walkthrough of the environment, which costs us roughly two weeks of eng time. Low probability, real impact.
- **Evidence-collection fatigue.** GRC platforms automate roughly 60 to 70 percent of evidence collection. The remainder is manual quarterly tasks (access reviews, vendor reviews, tabletop write-ups) that tend to slip when the team is busy. The compliance-eng squad named below owns the calendar for these tasks, and slippage is the first thing the monthly steering review checks.

## Operating Model During the Observation Window

A few notes on how the team will actually run this. They are here because compliance programs fail in operations, not in design.

- **Named squad.** A compliance-eng squad is carved from the existing org: 2 backend engineers, 1 SRE, 0.5 engineering manager. The squad is the standing owner of all eight capabilities through the design phase and the on-call owner of compliance regressions through the observation window.
- **Monthly steering review.** VP Eng, CISO, Compliance Lead, and the squad EM meet monthly. The agenda is fixed: control health dashboard from the GRC platform, exceptions opened and closed, evidence-collection lag, upcoming quarterly task readiness, budget burn, and risk register review.
- **Change management interface.** Every production change continues to flow through the existing CI/CD pipeline. The change-management control is implemented by lightweight metadata on the existing PR template (risk class, customer impact, rollback plan). We are not introducing a separate change board.
- **Break-glass.** Break-glass access remains available for incidents. Every break-glass invocation generates a high-priority audit event and a same-day post-use review. The auditor is fine with this pattern provided the review evidence is preserved.

## Appendix: Mapping of Capabilities to TSC

| Capability | Primary TSC mapping |
|---|---|
| 1. Centralized audit logging | CC7.2, CC7.3 |
| 2. Least-privilege RBAC | CC6.1, CC6.3 |
| 3. Encryption at rest (incl. backups) | CC6.7 |
| 4. Quarterly access reviews | CC6.2 |
| 5. Vulnerability management SLAs | CC7.1 |
| 6. TPRM program | CC9.2 |
| 7. IR runbook and tabletops | CC7.4, CC7.5 |
| 8. Trust center | CC2.3 |

End of document.
