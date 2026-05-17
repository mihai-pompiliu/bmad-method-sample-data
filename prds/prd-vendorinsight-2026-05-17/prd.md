# VendorInsight PRD

**Status:** Draft
**Owner:** Procurement Product Team
**Last updated:** 2026-05-17

---

## Problem / Background

Procurement decisions at our company are made across a sprawl of Excel workbooks, shared inboxes, and one-off scoring templates that vary by category and buyer. There is no single source of truth for how vendors compare on the dimensions that actually drive sourcing outcomes: cost, risk exposure, delivery reliability, and sustainability. Each annual audit becomes a multi-week scramble to reconstruct decisions from email threads and stale spreadsheets.

As the company has grown past 500 employees and our active vendor list has crossed several thousand, the cost of this fragmentation has become real: inconsistent scoring across categories, duplicated vendor records, missed renewal windows, and audit findings tied to traceability gaps. VendorInsight is an internal tool that consolidates vendor scoring and comparison into one place, with structured scoring rubrics, comparison views, and an auditor-facing read-only mode.

## Goals

- Replace the existing Excel-based vendor scoring workflow with a single internal tool used by all procurement managers and category buyers.
- Provide a consistent, configurable scoring rubric across four dimensions: cost, risk, delivery reliability, and sustainability.
- Give the annual auditor a read-only, time-bounded view of vendor scores and the decisions tied to them.
- Reduce time spent assembling vendor comparison reports for sourcing decisions and audit prep.

## Personas

**Priya, Procurement Manager.** Owns a category portfolio and is accountable for sourcing decisions across her categories. Needs to compare shortlisted vendors quickly, document the rationale for a selection, and report rollups to her director. Today she maintains a master spreadsheet that she manually reconciles against her buyers' working copies.

**Marcus, Category Buyer.** Works inside one or two categories and is closest to the day-to-day vendor relationships. Enters scores, attaches supporting evidence (quotes, performance data, certifications), and flags risks. Frustrated by inconsistent templates and rework when a manager asks for a comparison in a different format.

**Dana, Annual Auditor.** External (or internal audit team) reviewer who needs to verify that vendor decisions over the past fiscal year were made consistently and were traceable. Wants read-only access to historical scores, comparisons, and the evidence attached to each, scoped to a defined audit window.

## Functional Requirements

**FR-1: Vendor Records.** The system shall maintain a vendor record containing identifying information, category assignment, status (active, inactive, under review), and ownership (assigned buyer and manager).

**FR-2: Scoring Rubric.** The system shall support a configurable scoring rubric covering four dimensions: cost, risk, delivery reliability, and sustainability. Each dimension shall have weighted sub-criteria that procurement leadership can adjust.

**FR-3: Score Entry.** Category buyers shall be able to enter and update scores for vendors they own, including a numeric value, a short justification, and optional evidence attachments.

**FR-4: Score Review and Approval.** Procurement managers shall be able to review buyer-submitted scores and either approve them or return them with comments before they are counted as official.

**FR-5: Vendor Comparison View.** Users shall be able to select two or more vendors within a category and view a side-by-side comparison across all four scoring dimensions, including sub-criteria breakdown and overall weighted score.

**FR-6: Shortlist and Decision Record.** Procurement managers shall be able to create a shortlist for a sourcing decision, select a chosen vendor, and capture a written rationale that is permanently associated with that decision.

**FR-7: Evidence Attachments.** The system shall allow users to attach supporting documents (quotes, certifications, performance reports) to a score or a decision record, with file type and size limits enforced.

**FR-8: Search and Filter.** Users shall be able to search vendors by name and filter by category, status, owner, score range, and risk flag.

**FR-9: Auditor View.** The system shall support a read-only auditor role that can view vendor records, scores, comparisons, decisions, and attached evidence, scoped to a configurable audit window (typically the prior fiscal year).

**FR-10: Export.** Users shall be able to export a vendor comparison or a decision record to PDF for sharing with stakeholders who do not have system access.

## Non-Functional Requirements

- **Authentication:** All access shall be via the company's existing SSO provider. No local accounts.
- **Authorization:** Role-based access with at least four roles: buyer, manager, admin, auditor. Auditor access shall be time-bounded and revocable.
- **Audit log retention:** All score changes, approvals, and decision records shall be retained in an immutable audit log for a minimum of seven years.
- **Performance:** The vendor list view and comparison view shall load within two seconds for a vendor catalog of up to 10,000 vendors.
- **Availability:** Target 99.5% availability during business hours across the company's primary operating regions.
- **Data residency:** Vendor and decision data shall be stored in the company's existing approved cloud region.

## Non-Goals

- **Contract lifecycle management.** VendorInsight will not store, redline, or manage the lifecycle of contracts. The existing CLM system remains the source of truth for contracts.
- **Purchase order or invoice processing.** Transactional procurement (POs, invoices, payments) stays in the ERP. VendorInsight is a scoring and decision tool, not a transactional one.
- **Vendor-facing portal.** Vendors will not have logins or self-service access in v1. All data entry is performed by internal staff.
- **Automated risk scoring from external data feeds.** v1 will rely on buyer-entered scores. Integration with third-party risk data providers is out of scope for this release.

## Success Metrics

- **Adoption:** At least 90% of active categories have all sourcing decisions of $50K or greater recorded in VendorInsight within six months of launch.
- **Audit prep time:** Time spent assembling materials for the annual procurement audit is reduced by at least 60% compared to the prior year baseline (measured in person-hours).
- **Decision traceability:** 100% of recorded sourcing decisions of $50K or greater have an associated rationale and at least one piece of attached evidence.
