# Business Requirements Document: Month-End Close Acceleration Program

**Document ID:** BRD-FIN-MEC-2026-05
**Date:** 2026-05-17
**Author:** Office of the Corporate Controller
**Status:** Draft for FP&A and IT review
**Classification:** Internal, SOX-relevant

---

## 1. Executive Summary

The corporate close cycle currently runs eight to ten business days, which is no longer defensible against peer benchmarks or the expectations of our external auditors. Following the Q1 reporting cycle, the Audit Committee directed the Controller's office to deliver a sustainable Day-5 close by the end of FY2026, with a credible glide path toward a faster cadence thereafter.

This document defines the business requirements for the Month-End Close Acceleration Program (MECAP). It is intended to drive joint scoping between the Controller's organization, FP&A, Internal Audit, and Enterprise IT. It is not a technical design document and does not prescribe implementation details beyond those required to preserve SOX control integrity.

The program will modify the close orchestration layer that sits on top of NetSuite, the firm's ERP of record, and will tighten the workflow between sub-ledger owners, the consolidation team, and the SEC reporting group.

## 2. Business Drivers

- **Day-5 close target.** The Audit Committee has set a Day-5 close as a board-level KPI for the Controller beginning with the Q3 2026 close. Current run-rate is Day-8 with frequent slippage to Day-9.
- **SOX implications.** Manual journal entry handling, spreadsheet-based reconciliations, and informal email approvals have generated three control deficiencies in the last two 302/404 cycles. Remediation is non-negotiable.
- **Auditor friction.** External auditors have flagged the inability to produce a tamper-evident audit trail for late-cycle journal adjustments. This has materially increased audit fees in each of the last two fiscal years.
- **Restatement risk.** Two near-miss adjustments in FY2025 were caught only during the auditor's tie-out. A repeatable, system-enforced tie-out to the consolidated trial balance is required.
- **Talent retention.** The close team is working unsustainable hours during close week. While accountant well-being is a secondary concern of this program, attrition during close week is a SOX risk in its own right.

## 3. Stakeholders

| Stakeholder | Role | Interest |
|---|---|---|
| Corporate Controller | Program Sponsor | Owns close cycle, signs sub-certifications |
| Assistant Controller, Consolidations | Business Owner | Daily operator of close orchestration |
| VP FP&A | Consumer | Receives the closed books for variance and forecast work |
| Director, SOX & Internal Audit | Control Owner | Owns 302/404 control narratives |
| Director, IT Finance Systems | Delivery Owner | Owns NetSuite, integrations, and access controls |
| External Audit Engagement Partner | Reviewer | Reviews controls, journal sample selections, tie-outs |
| Tax Director | Consumer | Needs locked period before tax provision work |
| Treasury | Contributor | Provides cash and intercompany settlement data |
| Subsidiary Controllers (x6) | Contributors | Submit local trial balances and supporting schedules |

## 4. Scope

### 4.1 In Scope

- Journal entry intake, review, and posting workflow for all corporate and subsidiary entities.
- Sub-ledger reconciliation status tracking (AR, AP, Fixed Assets, Inventory, Payroll, Lease).
- Intercompany elimination tracking and aging.
- Period lock and controlled unlock procedures.
- Materiality-based approval routing.
- Tie-out from sub-ledgers and entity trial balances to the consolidated trial balance.
- Audit evidence package generation for the external auditor.

### 4.2 Out of Scope

- Replacement of NetSuite as the ERP of record.
- Tax provision calculation (handled by OneSource, downstream of close).
- Statutory reporting for non-US entities beyond providing locked figures.
- FP&A forecasting and budgeting workflows.
- Equity compensation accounting engine (handled in Workday).

## 5. Functional Requirements

The system shall provide the following capabilities. Requirements are stated at the business level. Implementation choices are deferred to the design phase except where explicitly noted.

- **FR-001** The system shall provide a single close calendar visible to all close participants, with task owners, dependencies, and target completion times stated in the controller's local time zone.
- **FR-002** The system shall enforce sequential task dependencies such that downstream tasks cannot be marked complete until their prerequisites are closed.
- **FR-003** The system shall capture every journal entry with preparer, reviewer, approver, posting timestamp, supporting documentation reference, and a non-editable change history.
- **FR-004** The system shall route journal entries for approval based on a materiality threshold matrix maintained by the Controller, with at minimum the bands: under $25,000, $25,000 to $250,000, $250,000 to $1,000,000, and above $1,000,000.
- **FR-005** The system shall require dual approval for any journal entry above $250,000 and Controller approval for any entry above $1,000,000.
- **FR-006** The system shall display sub-ledger reconciliation status for each in-scope sub-ledger as one of: Not Started, In Progress, Submitted, Reviewed, Approved, or Exception.
- **FR-007** The system shall flag any sub-ledger reconciliation with an unreconciled variance greater than $10,000 or 0.5 percent of the sub-ledger balance, whichever is lower.
- **FR-008** The variance report shall present columns in the following order: Account, Entity, Prior Period Balance, Current Period Balance, Dollar Variance, Percent Variance, Explanation, Preparer, Reviewer. No other column ordering shall be permitted in the standard view.
- **FR-009** The system shall track intercompany eliminations by counterparty pair and shall flag any unreciprocated balance aged greater than one close cycle.
- **FR-010** The system shall support an accrual review workbench listing all recurring accruals, last-period amount, current-period proposed amount, dollar and percent change, and reviewer sign-off.
- **FR-011** The system shall support the locking of a closed period such that no posting, edit, or deletion can occur without a documented unlock event.
- **FR-012** The system shall require Controller-level approval and a written justification for any post-lock unlock, and shall notify Internal Audit on unlock.
- **FR-013** The system shall use Blackline as the reconciliation engine.
- **FR-014** The system shall tie out each entity trial balance to the consolidated trial balance and shall not permit consolidation to be marked complete while any tie-out exception is open.
- **FR-015** The system shall produce an external auditor evidence package per close, including the journal entry register, approval evidence, reconciliation status, unlock log, and tie-out report.
- **FR-016** The system shall support segregation of duties such that no single user can prepare, approve, and post the same journal entry.
- **FR-017** The system shall maintain a top-side adjustment log distinct from the operational journal entry register, with mandatory CFO acknowledgment for any top-side adjustment above $500,000.
- **FR-018** The system shall achieve a continuous close.
- **FR-019** The system shall provide configurable email and in-app notifications for task assignment, approval pending, and SLA breach.
- **FR-020** The system shall retain all close artifacts for a minimum of seven years in a form retrievable by the external auditor without IT intervention.

## 6. Non-Functional Requirements

- **NFR-001** The system shall maintain 99.9 percent availability during close week (defined as business day minus 2 through business day plus 7 of each close cycle).
- **NFR-002** The system shall maintain a complete, tamper-evident audit trail for all financial transactions and workflow events.
- **NFR-003** The system shall enforce role-based access control aligned to the firm's SOX role matrix, with quarterly access recertification.
- **NFR-004** The system shall support single sign-on via the corporate identity provider and shall require MFA for any user with posting or approval authority.
- **NFR-005** The system shall return any standard close dashboard within three seconds at the 95th percentile during close week.
- **NFR-006** All data at rest and in transit shall be encrypted using current corporate cryptographic standards.

## 7. Assumptions

- NetSuite remains the ERP of record for the duration of this program and for the foreseeable planning horizon.
- The current chart of accounts and entity structure are stable through FY2026. No major M&A integrations are assumed during the program window.
- Subsidiary controllers will continue to submit local trial balances on the current schedule and in the current format.
- The external auditor will accept system-generated evidence packages in place of the current manual binder, subject to a walkthrough.
- IT will provide a non-production environment that mirrors production controls for SOX testing.

## 8. Risks

- **R-01** Failure to deliver Day-5 close by Q3 2026 would represent a missed board-level KPI for the Controller.
- **R-02** Misconfiguration of approval thresholds or segregation of duties could create a new SOX control deficiency rather than remediate the existing ones.
- **R-03** Subsidiary controller adoption is historically slow. Without an enforced cutover, the firm risks running parallel processes indefinitely.
- **R-04** Period unlock controls, if too rigid, may prevent legitimate late adjustments and push errors into the next period.
- **R-05** Auditor acceptance of the new evidence package is not guaranteed and must be validated before the Q3 close.
- **R-06** Integration latency between NetSuite, the reconciliation engine, and the close orchestration layer could undermine the Day-5 target.

## 9. Success Criteria

- Close-day count of five business days or fewer for two consecutive quarterly closes.
- Zero new SOX control deficiencies identified in the next 404 cycle attributable to close process or systems.
- Zero restatements and zero material post-close adjustments in the FY2026 annual cycle.
- External audit fees related to close testing reduced relative to FY2025 baseline.
- One hundred percent of in-scope journal entries routed through the system-enforced approval workflow, with no out-of-band approvals accepted.

## 10. Sign-off

| Name | Title | Signature | Date |
|---|---|---|---|
|  | Corporate Controller |  |  |
|  | VP Financial Planning and Analysis |  |  |
|  | Director, SOX and Internal Audit |  |  |
|  | Director, IT Finance Systems |  |  |
|  | Chief Financial Officer |  |  |
