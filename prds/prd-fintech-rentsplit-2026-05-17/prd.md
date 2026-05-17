# RentSplit PRD

## Document Control

| Field | Value |
|-------|-------|
| Product | RentSplit (consumer rent-splitting fintech) |
| Version | 0.4 (pre-launch, partner-bank review draft) |
| Date | 2026-05-17 |
| Status | In review with Risk and Partner Bank Liaison |
| Authors | M. Okafor (PM), S. Pereira (Compliance Lead) |
| Reviewers | Risk, Legal, Partner Bank Liaison (Tributary Bank), Eng Lead, Design Lead |
| Distribution | Internal + Tributary Bank Program Management |

## 1. Executive Summary

RentSplit removes the recurring social friction of rent splitting between roommates. One designated lead-tenant pays the landlord on a fixed monthly schedule from their own linked bank account. Roommates pay their share to the lead-tenant on a sub-schedule (default: one business day before the landlord debit) so the lead-tenant is whole before paying out. The product replaces the chronic "did you Venmo me yet" message with a scheduled, transparent, opt-in flow.

The product is free for renters at the base tier. Revenue comes from (1) a small flat fee per roommate sub-payment absorbed against float-interest margin held at the partner bank, and (2) an optional $4/month Pro tier (early-warning notifications, splitting non-rent household bills, annual export bundle).

RentSplit is the FinTech program; Tributary Bank, Member FDIC, is the sponsor bank of record and holds all customer funds. RentSplit does not take custody of money.

## 2. Regulatory Framing

The regulatory shape of this product is the gating factor on scope, geography, and time-to-launch. The PM and Compliance Lead co-own this section, and it should be read first by anyone reviewing downstream sections, because almost every constraint elsewhere in this document traces back to a posture decision made here.

### 2.1 State money-transmitter posture
- v1 operates only in states where Tributary Bank's existing money-transmitter coverage extends to RentSplit's program model (agent-of-the-payee construct + bank-held funds).
- Two states (TX, NY) require RentSplit to pursue its own MTL on a parallel track because partner-bank coverage is insufficient for our consumer-facing flow. Filings are in progress; v1 launches in TX and NY under partner-bank coverage with a contingency to suspend if MTL is denied.
- MA is excluded from v1. The state's MTL interpretation around hold-and-forward consumer flows is unresolved with Tributary's counsel. Re-evaluate at v2.

### 2.2 Federal posture
- Reg E applies to all consumer electronic transfers initiated through RentSplit. Disclosures, error-resolution timelines, and provisional credit rules are owned by Compliance.
- BSA/AML program runs under Tributary Bank's compliance umbrella with RentSplit performing first-line monitoring. OFAC screening at onboarding and on a recurring rescreen cadence.
- 1099-K reporting applies to lead-tenants receiving roommate sub-payments above the federal threshold in aggregate per calendar year. RentSplit issues 1099-Ks; this is a known UX surface (see 5.10).

### 2.3 Partner-bank arrangement
- Tributary Bank holds all program funds in an FBO (For Benefit Of) structure. Customer balances are pass-through; we do not run a stored-value model in v1.
- Tributary owns the BSA Officer relationship. RentSplit's Compliance Lead reports into program governance jointly.
- Program agreement caps transaction size, daily volume, and onboarding velocity (see Section 8).

### 2.4 Consumer-protection posture
- All consumer-facing disclosures pass a plain-language review before publication. Reg E error-resolution notices follow the model forms with minimal deviation.
- Complaints received via CFPB, state AGs, or BBB are routed to a single intake queue with a 5-business-day acknowledgment SLA.

## 3. Target User

Primary: US renter, ages 22 to 38, sharing a unit with one to four roommates, paying rent in the $800 to $4,500 per-person range. Already uses Venmo, Zelle, or Cash App for ad-hoc roommate transfers. Pain is recurring, low-grade, and social, not catastrophic.

Two roles inside the household:
- Lead-tenant: legally on the lease, has the landlord relationship, has the bank account that pays the landlord.
- Roommate: pays a share to the lead-tenant. May or may not be on the lease.

Out of scope for v1: master-tenant subletting arrangements with more than five sub-tenants, corporate housing, co-living operators.

## 4. Capability Surface (Functional Requirements)

### 4.1 Lead-tenant onboarding
- KYC tier 1 at signup: legal name, residential address, DOB, last 4 of SSN. Full SSN required at first money movement or if risk model flags.
- Bank account linking via Plaid. Manual micro-deposit fallback if Plaid fails.
- Landlord recipient setup: payee name, ACH routing + account, monthly amount, due day, lease end date.

### 4.2 Roommate invite and onboarding
- Lead-tenant invites roommate via SMS or email link.
- Roommate completes KYC tier 1 before any sub-payment can be scheduled. No "pay first, verify later" path.
- Plaid bank link required. Manual entry blocked in v1 to reduce mule and synthetic-identity risk.

### 4.3 Split configuration
- Equal split (default), custom percentages, or fixed amounts. Must sum to 100 percent of the landlord obligation. If lead-tenant absorbs the remainder, that is explicit and disclosed.
- Re-split allowed mid-lease (roommate joins, leaves, rent changes) with all-party in-app acknowledgment.

### 4.4 Sub-payment schedule
- Default: roommate ACH debit posts one business day before the landlord ACH debit.
- Configurable shift: up to three business days earlier for risk-averse households.
- Same-day ACH used where origination cutoffs allow; standard ACH otherwise.

### 4.5 Failed-payment handling
- Retry policy: one automated retry at 48 hours. Insufficient-funds returns (R01) trigger a roommate notification and a lead-tenant escalation.
- If retry fails, the lead-tenant is notified before the landlord debit is initiated so they can decide whether to cover the gap or pause the landlord debit.
- Landlord debit is never auto-paused by us. That decision belongs to the lead-tenant (legal counterparty risk).

### 4.6 Pause and resume
- Lead-tenant can pause an entire household (sublet, lease gap, dispute with landlord).
- Roommate can pause their own sub-payment only with lead-tenant acknowledgment.

### 4.7 Annual receipts
- Tax-time export of all sub-payments sent and received per household.
- 1099-K issued to lead-tenants who cross the federal reporting threshold in aggregate.

### 4.8 Dispute (roommate-to-lead)
- Roommate can dispute a sub-payment within 60 days. Dispute opens a Reg E investigation if the disputed transaction is an unauthorized EFT.
- Provisional credit issued within 10 business days per Reg E where investigation is unresolved.
- Disputes that are actually interpersonal disagreements (the roommate did the dishes, the lead-tenant should owe them) are out of scope and routed to a help article.

### 4.9 Pro tier
- $4 per month. Adds early-warning notifications (predicted insufficient funds based on historical balance signal from Plaid), the ability to split non-rent utility bills inside the same household, and a consolidated annual export bundle.

### 4.10 Notifications
- Transactional notifications (debit scheduled, debit posted, debit failed) are non-marketing and required.
- Marketing notifications are opt-in. CAN-SPAM and TCPA controls apply.

## 5. Risk and Fraud Surface

### 5.1 KYC
- Tier 1 at signup (name, address, DOB, last 4 SSN). Step-up to tier 2 (full SSN, ID document) on any of: cumulative monthly inbound or outbound over $3,500, address mismatch, device-risk flag, OFAC near-match.

### 5.2 AML monitoring
- Structuring detection on sub-payments below reporting thresholds with unusual cadence.
- Velocity rules: max three households per user, max five roommates per household, max one lead-tenant role per user in v1.
- OFAC screening at onboarding and rescreen monthly.

### 5.3 Fraud posture
- Account takeover: device-binding, step-up auth on bank-account change, 24-hour cooling period before a new landlord recipient receives the first debit.
- Synthetic identity: vendor identity-decisioning at signup, behavioral signal post-signup.
- Mule risk: monitoring for the pattern where a roommate quickly cycles funds from one household into another.

### 5.4 Transaction limits (program-agreement enforced)
- Per sub-payment: $5,000.
- Per landlord debit: $20,000.
- Per user, daily aggregate: $10,000.
- Households requiring higher limits are routed to a manual-review queue and may be declined in v1.

### 5.5 Dispute flow
- Acknowledge within 1 business day.
- Investigate within 10 business days; extension to 45 with provisional credit per Reg E.
- Documented decisioning trail handed off to Tributary on request.

## 6. Partner-Bank Dependencies

- Tributary Bank holds all funds in FBO structure. We cannot move to a stored-value or multi-FI model without re-papering the program agreement.
- ACH origination runs through Tributary's processor. Cutoffs and same-day ACH windows are inherited.
- KYC vendor stack is jointly approved. We cannot swap vendors without 60-day notice and Tributary's risk sign-off.
- Reg E error resolution is operationally ours but legally shared. Tributary's BSA Officer has audit rights.
- Marketing claims about FDIC pass-through insurance must be reviewed by Tributary's legal before publication.

## 7. Constraints

### 7.1 Regulatory
- v1 in six states only: CA, NY, TX, FL, WA, IL. Selection driven by (a) partner-bank MTL coverage or RentSplit's pending MTL, (b) renter population density, (c) tractable consumer-protection regimes.
- MA excluded pending counsel resolution.
- No crypto on-ramps or off-ramps. Tributary's program agreement disallows it.

### 7.2 Partner-bank
- Transaction caps as listed in 5.4.
- Onboarding throughput cap: 1,500 new fully-KYC'd users per day in v1. Above this, queue with disclosed expected wait.
- Any change to the FBO structure requires 90-day partner-bank review.

### 7.3 Customer-impact
- No "pay landlord directly on behalf of the household" flow in v1. The lead-tenant is the legal counterparty to the landlord and we are not inserting ourselves into that relationship.
- No instant transfer in v1. Same-day ACH where the window allows; standard ACH otherwise. Surfaced clearly in UX.

## 8. Non-Goals

- Not a generic P2P payment product. Venmo, Zelle, and Cash App already serve that need; we are deliberately single-use-case.
- Not paying landlords on behalf of the household. The lead-tenant pays the landlord. We do not become the legal counterparty.
- Not handling security deposits, move-in funds, last-month rent at lease signing, or move-out reconciliation in v1.
- Not a credit-builder. We will not report rent-payment history to credit bureaus in v1 due to FCRA furnisher obligations and the operational lift required.
- Not international. US-only in v1.
- Not crypto in any form. Partner bank prohibits.
- Not a landlord-side product. Landlords are recipients, not users.

## 9. Success Signals

Primary signal: percentage of households that complete a second consecutive on-time landlord debit with all roommate sub-payments cleared on schedule. This is the proof that the product replaces the social-friction loop, not just a one-time novelty use.

Supporting signals:
- Activation: lead-tenant + at least one roommate fully KYC'd and scheduled within 14 days of lead-tenant signup.
- Retention: month-3 household retention above 70 percent (rent is recurring; churn here is a strong negative signal).
- Failed-payment rate: under 3 percent of sub-payments returned (R01 or otherwise) in steady state.
- Dispute rate: under 0.5 percent of sub-payments disputed. Higher would signal either fraud exposure or interpersonal-confusion UX bleeding into the Reg E channel.
- Pro conversion: 8 percent of active households on Pro by month 6.

## 10. Open Questions

1. Do we accept households where the lead-tenant is not legally on the lease (informal subletting)? Compliance leans no; Product wants a path. Decision needed before v1 lock.
2. TX MTL filing: if the application is delayed past launch, do we hold TX launch or launch under partner-bank coverage with a documented contingency? Awaiting outside-counsel memo.
3. 1099-K UX: when and how do we tell lead-tenants that they may receive a tax form for receiving roommate sub-payments? Surfacing this at signup may dampen activation; hiding it creates a tax-season surprise.
4. Plaid outages: what is the documented fallback during a multi-hour Plaid degradation? Manual micro-deposit is slow and breaks the day-before-landlord-debit guarantee.
5. Roommate-departs-mid-month: should the system pro-rate automatically, or require explicit lead-tenant approval? Legal prefers explicit; UX prefers automatic.
6. Pro-tier utility-splitting: does pulling a utility bill via screen-scrape (where APIs do not exist) violate Tributary's vendor policy? Awaiting partner-bank guidance.
7. Marketing claim review: can we say "scheduled rent payments backed by an FDIC-insured bank" in paid acquisition copy, or must we use the longer "funds held at Tributary Bank, Member FDIC" form? Legal review pending.
