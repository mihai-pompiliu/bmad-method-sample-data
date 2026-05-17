# Business Requirements Document: Supplier Self-Service Portal

**Document ID:** BRD-PROC-2026-014
**Version:** 1.2 (Draft for IT Review)
**Date:** 2026-05-17
**Author:** Office of the Head of Procurement
**Sponsor:** VP, Procurement & Indirect Spend
**Recipient:** IT Delivery, Enterprise Applications Group

---

## 1. Executive Summary

Procurement currently maintains supplier master data, W-9 documentation, banking details, and invoice intake through a combination of email inboxes, shared network drives, and manual entry into the ERP. This model is not sustainable at our current operating scale of 2,400 active suppliers and approximately 18,000 supplier invoices processed annually across four manufacturing sites.

The business requires a Supplier Self-Service Portal (hereafter, "the system") that allows suppliers to maintain their own master data, submit tax documentation, transmit invoices, and view payment status without manual intervention from the AP team. The system shall integrate with the existing ERP (Oracle EBS R12) and the Treasury banking validation service. This document defines the business requirements only. Technical design, platform selection, and implementation approach are deferred to IT Delivery.

---

## 2. Business Objectives

- Reduce manual AP touch time per invoice by at least 40 percent within 12 months of go-live.
- Eliminate paper W-9 handling for new supplier onboarding.
- Achieve and maintain SOX-compliant audit trails for all supplier master data changes.
- Reduce supplier inquiry call volume to AP by at least 30 percent.
- Enable Procurement to scale to 3,500 active suppliers without additional AP headcount.

---

## 3. Stakeholders

| Role | Name | Department | Responsibility |
|------|------|------------|----------------|
| Executive Sponsor | M. Halverson | Procurement | Funding, escalation |
| Business Owner | R. Chen | Procurement | Requirements sign-off |
| AP Operations Lead | T. Okafor | Finance / AP | Process owner |
| Treasury Liaison | D. Briggs | Treasury | Banking validation |
| Tax Compliance | S. Patel | Tax | W-9 and 1099 oversight |
| Internal Audit | L. Romano | Audit | SOX controls review |
| IT Delivery Lead | TBD | IT Enterprise Apps | Solution delivery |
| Information Security | J. Vance | InfoSec | Security review |
| Supplier Representative (Advisory) | TBD | External | Advisory only |

---

## 4. Scope

### 4.1 In Scope

- Supplier onboarding and re-certification for all U.S.-based suppliers.
- Self-service maintenance of banking details, remit-to addresses, and contact information.
- Submission and storage of IRS Form W-9 and W-8 series.
- Invoice submission (PO-backed and non-PO).
- Payment status visibility for the supplier's own transactions.
- Integration with Oracle EBS R12 Accounts Payable and Supplier Master.

### 4.2 Out of Scope

- Sourcing, RFQ, and contract negotiation workflows.
- Catalog management and punch-out.
- International supplier onboarding for entities outside the U.S. (Phase 2).
- Direct materials suppliers governed by EDI (existing EDI flows remain).
- Mobile-native application development.

---

## 5. Functional Requirements

The following requirements are mandatory unless explicitly marked otherwise.

- **FR-001:** The system shall allow a prospective supplier to register and submit onboarding information without intervention from an AP clerk.
- **FR-002:** The system shall capture and store IRS Form W-9 in PDF format and shall extract the TIN, legal name, and entity classification for validation against the IRS TIN matching service.
- **FR-003:** The system shall reject any W-9 submission where the TIN/name combination fails IRS validation and shall record the rejection reason.
- **FR-004:** The system shall allow suppliers to update their banking information (routing number, account number, account type) and shall require dual verification before any such change is propagated to the ERP supplier master.
- **FR-005:** The system shall enforce a separation-of-duties control such that no single user, internal or external, can both submit and approve a banking change.
- **FR-006:** The system shall transmit validated supplier master changes to Oracle EBS R12 via the existing iSupplier interface or equivalent.
- **FR-007:** The system shall accept invoice submissions in PDF and structured (CSV or XML) formats.
- **FR-008:** The system shall validate PO-backed invoices against open purchase orders in the ERP and shall reject submissions where the PO is closed, cancelled, or fully consumed.
- **FR-009:** The system shall support non-PO invoice submission, subject to GL coding rules maintained by AP.
- **FR-010:** The system shall provide each supplier with visibility into the status of their own submitted invoices, limited to: Received, In Review, Approved for Payment, Paid, On Hold, Rejected.
- **FR-011:** The system shall display the scheduled payment date and payment method for any invoice approved for payment.
- **FR-012:** The system shall not expose any data belonging to other suppliers under any circumstance.
- **FR-013:** The system shall maintain a complete audit log of every change to supplier master data, including the user, timestamp, previous value, and new value, retained for a minimum of seven years.
- **FR-014:** The system shall require annual re-certification of supplier banking and tax information and shall flag suppliers whose certification has lapsed.
- **FR-015:** The system shall generate notifications to suppliers regarding submission status, rejections, and pending re-certifications.
- **FR-016:** The system shall be user-friendly and intuitive for suppliers of varying technical sophistication.
- **FR-017:** The system shall support attachment of supporting documentation (packing slips, receipts, signed delivery confirmations) to invoice submissions.
- **FR-018:** The system shall provide AP operations users with the ability to override or correct supplier-submitted data, with all overrides logged.
- **FR-019:** The system shall enable reporting on supplier onboarding throughput, invoice intake volume, rejection rates, and average time-to-pay.
- **FR-020:** The system shall be easy to use.

---

## 6. Non-Functional Requirements

- **NFR-001:** The system shall be available 99.5 percent during business hours (Monday-Friday, 06:00-20:00 Central Time).
- **NFR-002:** The system shall support a concurrent user load of at least 300 external supplier users and 60 internal users.
- **NFR-003:** The system shall process and acknowledge an invoice submission within 30 seconds of upload under normal load.
- **NFR-004:** The system shall encrypt all data in transit using TLS 1.2 or higher and all sensitive data at rest using AES-256 or equivalent.
- **NFR-005:** The system shall support single sign-on for internal users via the corporate identity provider.
- **NFR-006:** The system shall support multi-factor authentication for all external supplier accounts.
- **NFR-007:** The system shall comply with applicable provisions of SOX, IRS Publication 1075 (where applicable), and the corporate Information Security Standard.
- **NFR-008:** The system shall retain all transactional records for a minimum of seven years.
- **NFR-009:** The system shall be supportable by IT Operations using existing monitoring and incident management tooling.
- **NFR-010:** The system shall be performant.

---

## 7. Assumptions

- Oracle EBS R12 will remain the system of record for supplier master and AP through at least 2028.
- Treasury's existing bank account validation service will be made available via API to the new system.
- Suppliers have internet access and a modern browser.
- The IRS TIN matching service will remain available under our existing enrollment.
- IT Delivery will recommend the platform (build vs. buy, SaaS vs. on-premise) based on the requirements in this document.

---

## 8. Risks

| ID | Risk | Likelihood | Impact | Mitigation Owner |
|----|------|-----------|--------|------------------|
| R-1 | Supplier adoption is slow, delaying ROI | Medium | High | Procurement |
| R-2 | Banking change controls fail audit | Low | High | AP / Audit |
| R-3 | ERP integration scope exceeds estimate | Medium | High | IT Delivery |
| R-4 | Supplier data quality issues surface during migration | High | Medium | Procurement |
| R-5 | Phase 2 international scope creeps into Phase 1 | Medium | Medium | Sponsor |
| R-6 | Insufficient AP staffing during transition period | Medium | Medium | AP Operations |

---

## 9. Success Criteria

The project will be considered successful when the following measurable criteria are demonstrated for two consecutive quarters following go-live:

- At least 85 percent of active suppliers have registered and completed annual certification through the system.
- At least 90 percent of invoices are submitted through the system rather than via email or paper.
- 100 percent of supplier master data changes are captured in the audit log with no gaps identified by Internal Audit.
- Zero SOX control deficiencies identified related to supplier master or banking changes.
- AP manual touch time per invoice reduced by at least 40 percent versus the 2025 baseline.
- Supplier inquiry call volume to AP reduced by at least 30 percent versus the 2025 baseline.
- W-9 collection compliance reaches 100 percent for all 1099-eligible suppliers prior to year-end tax filing.
- Average time from invoice receipt to approval-for-payment reduced by at least 25 percent.

---

## 10. Sign-off

The undersigned acknowledge that the requirements stated in this document represent the business need and authorize IT Delivery to proceed with solution design.

| Name | Role | Signature | Date |
|------|------|-----------|------|
| M. Halverson | VP, Procurement (Sponsor) | __________________ | __________ |
| R. Chen | Director, Procurement Operations (Business Owner) | __________________ | __________ |
| T. Okafor | Senior Manager, Accounts Payable | __________________ | __________ |
| D. Briggs | Director, Treasury | __________________ | __________ |
| S. Patel | Director, Corporate Tax | __________________ | __________ |
| L. Romano | Senior Manager, Internal Audit | __________________ | __________ |
| J. Vance | Director, Information Security | __________________ | __________ |
| (TBD) | Director, IT Enterprise Applications | __________________ | __________ |

---

*End of document. Change requests against this BRD must be submitted in writing to the Business Owner and routed through the Procurement Change Control Board.*
