# Business Requirements Document: HR Onboarding Revamp

**Document ID:** BRD-HRO-2026-05-17
**Version:** 1.0 (Draft)
**Author:** VP, People Operations
**Date:** 2026-05-17
**Audience:** IT / HRIS Delivery Team
**Status:** Pending Sign-off

## 1. Executive Summary

The current new-hire onboarding process is fragmented across HRIS, IT ticketing, the shared drive, and a series of manual spreadsheets maintained by HR Business Partners. As the firm has scaled past 1,200 employees and continues to grow at roughly 18 percent year over year, the existing process is no longer sustainable. This BRD captures the business requirements for an integrated onboarding capability that automates pre-boarding, day-one readiness, and the first 90 days of the employee lifecycle. The solution must be modern and reflect our brand, integrate with our existing Workday tenant, and reduce HR coordinator effort per new hire.

## 2. Business Drivers

- Onboarding coordinator effort currently averages 4.6 hours per new hire; target is under 1.5 hours.
- Day-one IT readiness rate is 71 percent; the business requires 98 percent.
- Audit findings (Q4 2025) flagged I-9 retention gaps and inconsistent offer-letter versioning.
- Executive leadership has mandated a "white-glove yet scalable" onboarding experience.
- Anticipated 2026 hiring volume: 480 new hires, including 90 contractors converting to FTE.

## 3. Stakeholders

| Role | Name / Group | Responsibility |
|------|--------------|----------------|
| Executive Sponsor | CHRO | Final approval, budget authority |
| Business Owner | VP, People Operations | Requirements ownership, sign-off |
| Delivery Lead | Director, HRIS | Build and configuration |
| IT Partner | Director, Corporate IT | Provisioning, identity, endpoint |
| Security Partner | CISO Office | Access controls, data handling review |
| Legal Partner | Employment Counsel | I-9, offer letter, e-signature compliance |
| Learning & Development | Sr. Manager, L&D | Learning-path content and assignment rules |
| End Users (indirect) | Hiring Managers | Checklist execution |
| Procurement | Manager, IT Procurement | Equipment fulfillment |

## 4. Scope

### 4.1 In Scope

- Pre-boarding from offer acceptance through day one.
- Day-one through day-90 structured onboarding activities.
- Integration with Workday (system of record), Okta, ServiceNow, DocuSign, and the LMS.
- US-based hires (W-2 and 1099). Canada to be considered in Phase 2.
- Manager-facing checklists and HRBP dashboards.

### 4.2 Out of Scope

- Recruiting / applicant tracking (handled in Greenhouse).
- Offboarding workflows.
- International (non-US, non-Canada) hires.
- Performance management and compensation reviews.
- Contractor onboarding for sub-30-day engagements.

## 5. Functional Requirements

- **FR-001** The system shall generate offer letters from approved templates populated with Workday requisition data.
- **FR-002** The system shall deliver offer letters to candidates via secure, branded email within 15 minutes of HRBP approval.
- **FR-003** The system shall support legally binding e-signature via DocuSign and store the executed document in the employee record.
- **FR-004** The system shall collect Section 1 of Form I-9 from the new hire prior to day one and route Section 2 to the designated authorized representative.
- **FR-005** The system shall allow self-service equipment selection by the new hire from a catalog of approved configurations.
- **FR-006** The system shall route equipment provisioning requests to IT Procurement with required shipping address and target delivery date.
- **FR-007** The system shall trigger day-one system access provisioning in Okta, including email, SSO group assignment, and role-based application entitlements, no later than 24 hours prior to start date.
- **FR-008** The system shall assign role-based and compliance-based learning paths in the LMS within one business day of the start date.
- **FR-009** The system shall not allow new hires to make independent equipment decisions; all hardware selections must be determined by role and approved by IT Procurement.
- **FR-010** The system shall present hiring managers with a structured checklist covering pre-boarding, week one, day 30, day 60, and day 90 activities.
- **FR-011** The system shall send automated reminders to managers for overdue checklist items, escalating to the HRBP after five business days.
- **FR-012** The system shall distribute 30-day, 60-day, and 90-day pulse surveys and route low-score responses to the HRBP for follow-up.
- **FR-013** The system shall provide HRBPs with a dashboard view of all in-flight onboardings, filtered by start date, business unit, and status.
- **FR-014** The system shall maintain an immutable audit trail of all onboarding events for a minimum of seven years.

## 6. Non-Functional Requirements

- **NFR-001** Availability shall be 99.5 percent during business hours (06:00 to 20:00 ET).
- **NFR-002** All personally identifiable information shall be encrypted in transit (TLS 1.2+) and at rest (AES-256).
- **NFR-003** Access shall be governed by role-based access control aligned to existing Okta groups.
- **NFR-004** The solution shall comply with SOC 2 Type II, GDPR (for transfer cases), and applicable state privacy laws (CCPA, CPRA, NYSHIELD).
- **NFR-005** The onboarding portal shall be accessible per WCAG 2.1 AA.
- **NFR-006** The solution shall be mobile-responsive across iOS Safari and Android Chrome.
- **NFR-007** End-to-end transactions (e.g., offer-letter generation) shall complete within 5 seconds at the 95th percentile.
- **NFR-008** All integrations shall use authenticated, logged API calls. No direct database access between systems.

## 7. Assumptions

- Workday remains the authoritative source for employee master data.
- DocuSign enterprise license capacity is sufficient for projected volumes.
- IT Procurement maintains a current and accurate equipment catalog.
- New hires have access to a personal email and an internet-connected device during pre-boarding.
- L&D will deliver finalized learning-path mappings by 2026-07-15.

## 8. Risks

| ID | Risk | Likelihood | Impact | Mitigation |
|----|------|------------|--------|------------|
| R-01 | Workday API rate limits constrain real-time provisioning | Medium | High | Implement queueing and retry; engage Workday TAM |
| R-02 | Equipment fulfillment lead times exceed start dates | High | High | Forecast 30 days ahead; maintain buffer inventory |
| R-03 | I-9 Section 2 completion delays for remote hires | Medium | Medium | Expand authorized-representative network |
| R-04 | Change management resistance from HRBP community | Medium | Medium | Early involvement, phased rollout, training |
| R-05 | Scope expansion to include offboarding mid-project | Medium | High | Formal change-control governance |

## 9. Success Criteria

- Time-to-productive (defined as completion of role-based learning path) reduced from 38 days to 21 days.
- Onboarding checklist completion rate of 95 percent at day 30.
- Day-one system access readiness of 98 percent or higher.
- I-9 Section 2 completion within federally mandated three business days at 100 percent.
- HR coordinator hours per new hire reduced to under 1.5.
- Audit findings related to onboarding documentation reduced to zero in the next internal audit cycle.
- Survey distribution and capture rate of 85 percent at 30, 60, and 90 days.

## 10. Sign-off

| Role | Name | Signature | Date |
|------|------|-----------|------|
| Executive Sponsor (CHRO) | | | |
| Business Owner (VP, People Operations) | | | |
| Delivery Lead (Director, HRIS) | | | |
| IT Partner (Director, Corporate IT) | | | |
| Security Partner (CISO Office) | | | |
| Legal Partner (Employment Counsel) | | | |
