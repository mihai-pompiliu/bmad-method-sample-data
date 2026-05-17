# Calmleaf Pivot PRD: From Consumer Journaling App to B2B Wellness Platform

**Document type:** Strategic pivot PRD
**Author:** CEO / co-founder
**Audience:** Board, leadership team, engineering, GTM
**Status:** Draft for board review
**Date:** 2026-05-17

## Change governance

This is not a feature PRD. This is a pivot. Changes to this document require sign-off from the board (strategic direction, runway allocation) and from the CEO (scope and sequencing). Engineering and GTM leads own implementation detail under the kernel set here. Material deviations (timeline slip beyond 60 days, ARR target revision, ICP redefinition) trigger a re-review with the board.

## Background: what we were, and what stopped working

Calmleaf launched 18 months ago as a B2C mental-health journaling app. Core surface: a mobile app with a journaling engine, mood charts, and AI-assisted reflection prompts. Pricing was $7.99 per month, App Store and Play Store distribution, paid-acquisition driven (primarily Meta).

We grew to roughly 9,000 paying subscribers, peaking around $72K MRR. Growth has plateaued for two consecutive quarters. The proximate cause is CAC: blended CAC moved from $14 to $48 over the last quarter as Meta ad inventory tightened and consumer mental-health was reclassified into a more expensive auction segment. Payback periods that used to land inside 3 months are now past 9, which the unit economics will not absorb.

The deeper cause is that the consumer DTC playbook for category-mature wellness apps no longer works at our spend level. We are not Calm, we are not Headspace, and we cannot outspend either of them on the platforms that matter.

We have $1.4M of runway, which is approximately 9 months at current burn. Continuing the consumer motion is a controlled descent into insolvency. A pivot is the only responsible path.

## The pivot thesis

Three of our top-revenue consumer accounts turned out, on inspection, to be corporate wellness programs buying multiple seats through a single buyer login. They were paying us full retail per seat, manually onboarding employees, and tolerating a product that was not built for them. They were doing this because nothing in the corporate wellness category felt as good as Calmleaf to the end user.

That is the signal. There is a buyer in the market who wants what we built, will pay more for it, and is not subject to Meta's auction. We are going to go meet them.

We are pivoting Calmleaf from a B2C consumer subscription to a B2B SaaS sold to corporate wellness programs, employer-benefits buyers, and EAP providers. Target ACV $20K to $60K. ICP: companies of 500 to 5,000 employees with an existing wellness or benefits function. The product we already have is most of the employee-facing surface. The product we need to build is the buyer-facing surface around it.

## The new shape of the business

- Revenue model: per-seat annual licensing, minimum 50 seats, annual commit, invoiced.
- Buyer: Head of Benefits, Head of People, Wellness Program Manager, or EAP product owner.
- End user: still the employee, journaling on mobile. We are not changing the human experience of the product.
- Distribution: direct sales (founder-led initially), partnerships with benefits brokers and EAP networks, content marketing aimed at HR and benefits buyers.
- Success window: $300K committed ARR pipeline within 6 months from pivot launch, which extends runway to a fundable position.

## What carries over

These are the assets that survive the pivot intact. They are why we believe the pivot is feasible inside our runway.

- The core journaling engine. The data model, the editor, the offline sync, all reusable.
- Mood charting and longitudinal mood data. This becomes the foundation of the analytics layer the buyer wants.
- AI-assisted reflection prompts. Differentiated, well-tuned, and works.
- The mobile app surface (iOS, Android). This is the employee experience and it is good.
- Our content tone for end users (calm, non-clinical, non-judgmental). We keep this for the employee side.
- Infrastructure, observability, and our existing engineering team's familiarity with the codebase.

## What changes

These are the deltas required to make the product sellable to a B2B buyer.

- Pricing model: from $7.99 per month consumer subscription to per-seat annual licensing, 50-seat minimum, annual commit.
- Buyer-facing brand voice: less DTC ("breathe, you've got this"), more enterprise ("credible, measurable, calm"). The employee voice inside the app stays.
- Marketing site: replace the consumer-conversion landing page with an employer-buyer landing page, ROI calculator, case study slots, and a demo-request flow. Existing consumer page sunsets.
- Acquisition motion: from paid social to outbound sales, partnerships, and HR/benefits content marketing.
- Sales process: from self-serve checkout to founder-led sales with security review, procurement, and contracting cycles.

## What we are adding

These are net-new product capabilities required to clear the B2B bar.

- Tenant-isolated admin console for buyers. Seat management, deployment dashboard, billing visibility.
- SSO: SAML 2.0 and OIDC. Table stakes for the ICP.
- Aggregate, anonymized wellness analytics for HR. Engagement, opt-in rate, mood trend at population level. Never individual.
- Seat-based billing with usage caps and overage handling.
- Curated content library of corporate wellness modules (stress, burnout, manager support, life transitions). Curated, not authored by HR.
- BAA-readiness. We are not a covered entity, but we are journaling-adjacent to PHI. We need to be ready for the question.
- DPA template, security questionnaire pack, basic trust page.

## What we are retiring or freezing

These are deliberate subtractions. Calling them out explicitly so we do not accidentally keep maintaining them.

- Consumer App Store / Play Store subscription. Closed to new signups on pivot launch day. Existing subscribers grandfathered for 12 months per EULA.
- Consumer-facing referral program. Frozen, then retired.
- Consumer-side gamification (streaks, badges). Retired. Replaced with employer-friendly engagement signals (opt-in rate, sustained-use cohorts) that do not feel like a habit-loop app at work.
- Consumer paid-acquisition spend. Hard stop on pivot launch day.
- Consumer-conversion landing page and funnel. Replaced.

Open question, flagged for resolution before pivot launch: do we keep the name "Calmleaf" or rebrand for the enterprise audience. Carries brand equity but also carries DTC association. Decision owner: CEO, due before GTM launch.

## Required product changes (the delta, as functional requirements)

- FR-1: Multi-tenant data model. Every user record belongs to a tenant; tenant boundary is enforced at the data layer. Existing single-tenant consumer data migrates to a "legacy consumer" tenant for grandfathering.
- FR-2: Admin console (web). Buyer can invite, deactivate, and reassign seats. View deployment health.
- FR-3: SSO. SAML 2.0 (Okta, Azure AD, OneLogin as validated IdPs) and OIDC. SCIM provisioning deferred to v1.1.
- FR-4: Aggregate analytics. HR-facing dashboard showing tenant-level engagement and mood-trend aggregates. Minimum cohort size of 10 before any aggregate is shown, to protect individual privacy.
- FR-5: Seat-based billing. Annual commit, monthly true-up for overages, invoiced (not card-charged).
- FR-6: Content library shell. Curated modules surfaced inside the existing journaling experience. Authoring tool is out of scope.
- FR-7: Security and compliance surface. Trust page, DPA template, security questionnaire response pack, BAA-readiness checklist completed.
- FR-8: Consumer signup off-ramp. After pivot launch, the consumer signup path returns a "Calmleaf for Teams" page. Existing users retain full access via existing credentials.

## Constraints

- Runway: 9 months. Pivot must produce $300K committed ARR pipeline within 6 months, or we are in a different conversation with the board.
- Existing consumer obligation: 9K consumer users have grandfather rights via EULA, minimum 12 months. We continue to operate the consumer surface for them. No degradation.
- Sensitive content: journaling content is sensitive even where we are not legally a covered entity. We hold the HIPAA-adjacent bar even when not required.
- Team: 11 people. No new hires until pipeline traction is demonstrable. Pivot is executed with current team.
- Capital: no bridge round assumed inside the 6-month window. Pivot must work on cash on hand.

## Non-goals

Stated explicitly so we do not drift into them under pressure.

- Not a B2B2C model. We are not letting corporate buyers resell Calmleaf to consumers.
- Not peer-to-peer corporate features. No team journals, no shared entries, no manager visibility into individual content. Ever.
- Not pursuing K-12 or university market. Adjacent, real, deferred. Not in this pivot.
- Not building a clinical product. No therapist matchmaking, no clinical assessments, no diagnostic claims. Clear regulatory line, held.
- Not building a custom content authoring tool for HR teams. Curated library only in v1.

## Migration plan for existing consumer users

- Day 0 (pivot launch): Consumer signup closes. Existing subscribers continue uninterrupted. In-app communication explains the change, reassures continuity, makes no asks.
- Months 1 to 12: Continue serving existing consumer subscribers under current pricing and feature set. No degradation, no upsell, no nag.
- Month 9: Begin proactive outreach to consumer subscribers whose employers are existing or prospective tenants, offering managed migration onto an employer plan with consent.
- Month 12: Consumer plan reaches end of grandfather term. Subscribers receive 60-day notice, with options: migrate to an employer plan (if available to them), self-export their journal data, or wind down.
- Month 14: Consumer surface fully retired.

Data export is a hard requirement at every step. Subscriber owns their journal content.

## Success criteria

What "the pivot landed" looks like, in measurable terms.

- $300K committed ARR within 6 months of pivot launch. Committed means signed order forms, not pipeline-weighted.
- 6 to 10 ICP-fit logos in the first 12 months. ICP-fit means 500 to 5,000 employees and an in-house wellness or benefits function.
- Employer NPS of 30 or above among deployed tenants at month 6 post-deployment.
- Employee opt-in rate of 25% or above within deployed tenants at month 3 post-deployment. This is the leading indicator that the underlying product still works for end users.
- Gross retention 90% or above on first renewal cohort.

## Risks and rollback

- Risk: ICP fit is narrower than we believe and we burn 6 months learning that. Mitigation: founder-led sales for the first 10 conversations to compress the learning loop. Reassess ICP at month 3 if conversion is below expectation.
- Risk: Compliance surface (BAA, security review) blocks deals we would otherwise close. Mitigation: prioritize trust surface and DPA in the first 90 days, even at the cost of feature work.
- Risk: Existing consumer users churn loudly and damage the brand we are trying to sell. Mitigation: continuity-first communication, no degradation, full data export rights.
- Risk: Team morale. We told people we were building a consumer brand. Mitigation: this PRD is shared with the team, not just the board. Honesty about why, conviction about where.
- Rollback: there is no clean rollback. Once consumer acquisition is shut off, the consumer business decays on its own clock. The decision to pivot is the decision. If $300K committed ARR is not in sight at month 4, the conversation is not "rollback," it is "wind-down or sale of assets," and that conversation happens with the board.

## Open questions

- Brand: keep "Calmleaf" or rebrand for enterprise. CEO-owned, decide before GTM launch.
- Channel: direct-only for the first 6 months, or invest in broker / EAP partnerships in parallel. Revisit at month 3 based on outbound conversion.
- Pricing floor: is 50 seats the right minimum, or do we go higher to filter for ICP fit and avoid small-deal drag.
- AI prompt tuning for the workplace context. The reflection prompts were tuned for evenings-on-your-couch. They are not wrong at work, but they are not optimized for it. Open whether this is a v1 concern or a v1.1 concern.
- Data residency. We currently run in a single US region. First EMEA buyer conversation will surface a residency requirement. Decide whether we engineer for it pre-emptively or wait for the forcing function.

## Sequencing (high-level, 6 months)

- Month 0 to 1: Multi-tenant data model migration. Trust surface (DPA, security pack, trust page). Brand decision locked.
- Month 1 to 2: Admin console MVP. SSO (SAML first). Consumer signup off-ramp shipped. Pivot launch.
- Month 2 to 4: Aggregate analytics. Seat billing. Content library shell. First 5 design-partner deployments.
- Month 4 to 6: OIDC, second IdP coverage, broker / EAP outreach if direct conversion warrants. $300K committed ARR checkpoint with board.

Detailed sequencing, ownership, and dependencies live in the pivot execution plan, not this PRD. This document defines what we are building and why. The execution plan defines who and when.

## Decision log (to be appended)

This section is reserved. Material decisions made against this PRD (brand, ICP refinement, pricing floor changes, scope changes) will be appended here with date, decider, and one-line rationale. The PRD itself is not edited in flight; the log captures drift.
