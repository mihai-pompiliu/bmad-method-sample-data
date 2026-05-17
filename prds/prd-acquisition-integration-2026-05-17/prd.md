# PRD: Drift Compass Acquisition Integration

## Document Control

- Author: VP Corporate Development
- Co-author: VP Product
- Reviewers: CEO, CFO, CISO, CHRO, General Counsel
- Status: Approved for execution (post-close)
- Version: 1.0
- Close date: 2026-04-30
- Effective date: 2026-05-01

## Background

On 2026-04-30, Vesta (the company, $180M ARR, cloud-posture management) closed its acquisition of Drift Compass, a 22-person startup based around a developer-friendly secret-detection-in-code product. Drift Compass operated as a self-serve SaaS with approximately $4M ARR across 140 customers, weighted heavily toward SMB and individual developer teams.

Deal shape:
- Total consideration: $46M
- Mix: 60% cash, 40% Vesta equity (vesting over 4 years for founder and key engineers)
- Reps and warranties survive 18 months
- Founder plus 5 named engineers carry 18-month minimum employment commitments
- 14 engineers, 3 GTM, and the founder are joining Vesta
- 4 employees are being offered severance and 6-month transition roles (laid off, not retained for the integrated org)
- 1 employee declined the offer and exited at close

This PRD covers integration of Drift Compass into Vesta across product, technology, team, brand, and customer relationship surfaces.

## Strategic Rationale

Vesta's CSPM platform has a known gap: it observes misconfigurations in cloud infrastructure but does not catch secrets committed to source code, which is one of the most common entry points in cloud-related incidents. Three drivers:

1. Customer pull. 38 of Vesta's top 200 customers have asked for in-code secret scanning in the last 12 months.
2. Build vs. buy. Internal estimate was 14-18 months and 9 engineers to reach feature parity with Drift Compass.
3. Distribution leverage. Drift Compass has a credible developer brand and an OSS detector with meaningful GitHub presence. Vesta does not.

The acquisition collapses time-to-market to under 6 months for an integrated module, while bringing in a team with domain credibility.

## Integration Thesis

We are pursuing full product integration with brand retirement, not a tuck-under and not operate-as-subsidiary.

- Drift Compass's product becomes a module ("Code Risk") inside the main Vesta platform.
- The standalone Drift Compass SaaS is sunsetted on a 12-month timeline.
- The Drift Compass brand is retired by Day 365.
- The acquired team forms a new "Code Risk" business unit inside Vesta engineering.

Operate-as-subsidiary was rejected because the value thesis depends on Vesta customers seeing Code Risk as a native capability, not a bolt-on. Tuck-under (product only, team scattered) was rejected because the team's domain knowledge is concentrated and breaking it apart in year one carries unacceptable execution risk.

## Product Integration

In scope for integration into the main Vesta product:
- Secret detection engine (embedded as-is, no rewrite on Vesta's stack)
- Detector ruleset and signature library
- Repository connectors (GitHub, GitLab, Bitbucket)
- CLI and pre-commit hook integration
- Findings model, mapped into Vesta's existing finding/issue schema

Out of scope (kept separate or sunsetted):
- Drift Compass marketing site (redirects to Vesta by Day 90)
- Drift Compass standalone customer dashboard (sunsetted by Day 365)
- Drift Compass billing and trial signup flow (sunsetted by Day 180)

The integrated module ships under Vesta's enterprise tier with a board-approved attach price, and a new self-serve "Developer" SKU is created to absorb the SMB segment that drove Drift Compass's growth.

## Customer-Facing Changes

Changes (communicated Day 1 via letter from CEO and Drift Compass founder):
- Billing relationship moves to Vesta entity (new invoice format, new payment portal) on each customer's renewal or Day 180, whichever comes first.
- Support routes through Vesta's Zendesk after Day 30.
- The product roadmap shifts to integration with Vesta's platform.
- Brand transition begins Day 90 and completes Day 365.

What does not change (also in the Day 1 letter):
- Existing contracted pricing stays in force through current term.
- Detection engine and rule behavior are unchanged.
- API endpoints remain stable for 12 months minimum, with deprecation notices required 180 days before any breaking change.
- The OSS detector continues to be maintained.

Existing customers may keep paying for the standalone Drift Compass SaaS through its sunset on Day 365, or migrate to the Vesta Code Risk module at no incremental cost for the remainder of their current term.

## Technical Integration

| Surface | Current state | Target state | Target date |
|---|---|---|---|
| Authentication | Drift Compass local auth + Google SSO | Vesta SSO (Okta-backed) | Day 90 |
| Billing | Drift Compass Stripe account | Vesta Stripe account | Day 180 |
| Support | Intercom (Drift Compass) | Vesta Zendesk | Day 30 |
| Data plane | Drift Compass AWS account (us-east-1, eu-west-1) | Vesta AWS account (matched regions) | Day 180 |
| Infra (CI, observability) | Drift Compass GitHub Actions, Datadog | Vesta GitHub Actions, Datadog | Day 120 |
| Source control | Drift Compass GitHub org | Vesta GitHub org (Code Risk team) | Day 60 |
| Secrets / key management | Drift Compass AWS KMS | Vesta AWS KMS, with key rotation at migration | Day 180 |

Data residency for EU customers must be preserved without interruption. The eu-west-1 plane moves to Vesta's existing eu-west-1 footprint, not consolidated to us-east-1.

## Team Integration

Org structure post-close:
- Drift Compass founder becomes GM of the Code Risk business unit, reporting to the Vesta CTO.
- One incumbent Vesta engineering manager is moved to lead the integrated engineering team day-to-day. The founder retains business unit GM scope (product, GTM partnership, roadmap), while the Vesta EM owns engineering delivery.
- 14 engineers join the Code Risk team. The 3 GTM hires join Vesta's product marketing and developer relations teams respectively.

This split (founder as GM, Vesta EM as engineering lead) is the most sensitive piece of the integration. The founder was the engineering leader at Drift Compass. The framing in the founder's offer letter and the all-hands script is explicit: this is a scope expansion, not a demotion, and engineering management is delegated so the GM can run the business unit. The CHRO will run pulse checks at Day 30, Day 60, and Day 90.

Retention plan:
- Founder and 5 named engineers: 18-month employment commitments with stock vest acceleration tied to integration milestones (Day 90 product GA, Day 180 infra migration complete, Day 365 brand retirement).
- Remaining 9 engineers: 12-month retention bonus, half at Day 180, half at Day 365.
- 4 employees on transition: severance plus 6-month role; CHRO owns weekly check-ins.

## Brand and Naming

- Drift Compass brand is retired by Day 365. All customer-facing surfaces switch to "Vesta Code Risk" by that date.
- Internal engineering identity is preserved. The codebase, repos, internal Slack channels, and the engineering team use "Compass" as a module name. This is deliberate: it gives the acquired team a continued identity that customers do not see.
- The OSS detector continues to ship under the existing project name and license. Governance moves to the Code Risk business unit, with the founder as the project owner.

## Timeline

### Day 1 (2026-05-01)
- Customer letter sent. Internal all-hands at both Vesta and Drift Compass.
- Founder begins as GM. New org chart published internally.
- Retention agreements signed by named engineers.
- Press release issued.

### Day 30
- Support routed through Vesta Zendesk.
- Drift Compass customers have Vesta support contacts.
- F500 financial services customer BAA chain notification issued (90-day-notice MSA clause).
- Integration steering committee (CEO, CTO, GM, VP Product, General Counsel) holds first review.

### Day 90
- Vesta SSO live for Drift Compass customers.
- Drift Compass marketing site redirects to Vesta.
- Code Risk module enters limited GA inside Vesta platform.
- First retention milestone reached. First vest acceleration triggers.
- Customer satisfaction pulse for migrated cohort.

### Day 180
- Billing fully on Vesta Stripe.
- Data plane migration complete (US and EU).
- Second retention milestone. Second vest acceleration triggers.
- 50% of self-serve Drift Compass customers migrated to Developer tier on Vesta platform.

### Day 365
- Drift Compass brand fully retired.
- Standalone Drift Compass SaaS shut down. Remaining customers either migrated or churned with grace exit.
- Grandfathered month-to-month customer rights expire.
- Third retention milestone. Final vest acceleration triggers.
- Integration retrospective with full reviewer panel.

### Year 1 to Year 2
- Code Risk business unit operates as native Vesta surface.
- New roadmap items (beyond integration) begin.

## Constraints

Legal:
- Reps and warranties survive 18 months. Any material claim or risk that surfaces in this window goes to General Counsel before action.
- Founder and 5 named engineers have 18-month minimum employment commitments per their retention agreements. Termination without cause inside that window forfeits Vesta's claw-back rights and accelerates remaining vest.

Customer:
- 22 customers on legacy month-to-month subscription terms hold grandfather rights for 12 months. Their pricing, terms, and detection behavior cannot change inside that window without their written consent.
- One F500 financial services customer has a 90-day-notice clause in their MSA for any vendor change. Their BAA chain (Drift Compass to Vesta) requires explicit notification with security artifacts, signed by Vesta CISO, sent no later than Day 30.

Retention and team:
- The split of GM (founder) and engineering lead (Vesta EM) is the highest human risk surface. The CHRO owns pulse checks at Day 30, 60, 90. If founder retention is at risk, the CEO is paged immediately.
- The OSS detector cannot be deprecated, relicensed, or relocated without founder sign-off during the 18-month retention window. This is a stated condition of the founder's continued employment.

Timing:
- The integration steering committee meets monthly through Day 365. Any deviation from the timeline above requires a written change order and steering committee approval.

## Non-goals

We are not doing the following in year one. These are explicit because each has surfaced as integration creep in early planning.

1. We are not rebuilding the Drift Compass detection engine on Vesta's existing CSPM stack. The engine is embedded as-is. Re-platforming is on a future roadmap, not this integration.
2. We are not relocating the Drift Compass team. They are fully remote and stay fully remote. No relocation packages, no required office days.
3. We are not expanding the Code Risk product surface during integration. No dependency vulnerability scanning, no SAST, no IaC-as-code coverage in year one. Integration first, expansion later.
4. We are not changing the Drift Compass open-source policy. The OSS detector continues to ship under the existing license and project name, governed by the new business unit with the founder as project owner.
5. We are not running cross-sell campaigns into the Drift Compass customer base in the first 90 days. Sales is explicitly held back. Let the integration land before sales motions cross.
6. We are not migrating existing Drift Compass customers off their current contracts before renewal. They keep what they have until their term ends.
7. We are not consolidating EU customer data into US regions. EU data stays in eu-west-1, period.

## Success Criteria

By Day 90:
- 90% of acquired engineers still employed.
- Founder reports green or yellow on retention pulse (red triggers CEO escalation).
- Code Risk module in limited GA inside Vesta platform.
- Customer-reported issues from the migration cohort under 5% of installed base.

By Day 180:
- Billing, infra, and support fully consolidated to Vesta systems.
- 50% of self-serve Drift Compass customers migrated to Developer tier.
- Net revenue retention on the acquired book is 95% or better.
- Zero material legal or customer-contract claims arising from the integration.

By Day 365:
- Drift Compass brand fully retired.
- 85%+ of acquired engineers still employed (named retention group at 100%).
- Code Risk module attached to 25% of Vesta's enterprise customer base.
- Standalone Drift Compass SaaS shut down with no involuntary customer loss outside of normal churn.

By Year 1 end:
- Code Risk contributes $8M+ ARR to Vesta (organic plus migrated).
- Integration retrospective completes with no open material findings.

## Risks and Contingencies

| Risk | Likelihood | Impact | Contingency |
|---|---|---|---|
| Founder departs before 18 months | Low | High | Vesta EM steps up as interim GM. Vest claw-back enforced. Steering committee reviews business unit scope. |
| Engineering team attrition above 20% | Medium | High | Retention bonuses re-cut. CHRO leads stay-interview round. Integration timeline slips, not the team. |
| Self-serve customer churn above 25% during migration | Medium | Medium | Extend grandfather window. Pause sunset timeline by 90 days. |
| F500 customer triggers MSA termination | Low | High | General Counsel and CISO lead direct engagement. BAA chain notification is the primary mitigation. |
| Data migration causes EU customer outage | Low | High | Migration runs in shadow mode for 30 days before cutover. Rollback playbook signed off by CISO. |
| Internal Vesta EM and founder relationship deteriorates | Medium | High | CTO mediates. If unresolved by Day 90, restructure reporting lines. Documented in steering committee notes. |
| Integration creep (PMs push to expand scope) | High | Medium | Non-goals section above is the answer. VP Product enforces. Steering committee veto. |
| OSS community reaction to acquisition is hostile | Medium | Medium | Founder leads community communication. No license changes. Public roadmap commitments stay public. |

## Appendix: Open Items

- Final pricing for the Developer tier SKU (board review scheduled Day 14).
- Naming convention for migrated customer accounts in Vesta CRM (Sales Ops to propose by Day 30).
- Decision on whether to retain the Drift Compass domain after Day 365 or sunset entirely (General Counsel and Marketing to decide by Day 270).
