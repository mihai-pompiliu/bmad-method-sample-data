# Tidewater PRD

Scope 1/2/3 emissions accounting and disclosure-prep platform for mid-market companies.

## Document Control

- Product: Tidewater
- Version: 0.1 (draft, internal)
- Owner: Product (Climate vertical)
- Date: 2026-05-17
- Status: Pre-build, design partner phase
- Distribution: Internal product, engineering, GTM leadership; design-partner advisors under NDA
- Reviewers: Sustainability advisory board, Controller-track design partners, external assurance partner (Big Four climate practice)

## Regulatory Backdrop

The buyer is buying right now because the regulators showed up. This is a compliance-driven market, and the timing matters more than the product wedge.

- CSRD (Corporate Sustainability Reporting Directive). EU directive. Mandatory ESRS standards including ESRS E1 (climate). Applies to large EU companies and to non-EU companies with material EU operations. First-wave reporting already in motion in 2024-2025 fiscal years. Second and third waves pull in thousands more mid-market companies through 2027-2028.
- California SB 253 (Climate Corporate Data Accountability Act). Applies to companies doing business in California with over $1B in global revenue. Requires Scope 1 and 2 disclosure starting FY2025 (reported 2026) and Scope 3 the following year. California SB 261 adds a climate-related financial risk disclosure for companies over $500M.
- SEC climate disclosure rule. Adopted in 2024, paused in litigation. Status uncertain in 2026. We do not bet the roadmap on it, but we keep the data model compatible.
- Voluntary frameworks. CDP (Carbon Disclosure Project) annual submissions, SBTi (Science Based Targets initiative) target alignment, TCFD-aligned risk disclosure (now folded into ISSB IFRS S2).

The buyer's calendar is set by these regimes. Renewal risk for us is highest right after a customer submits their first report.

## Buyer Persona

Primary buyer is one of three roles, depending on company structure:

- Chief Sustainability Officer (CSO) or VP of Sustainability. Owns the disclosure. Often new to the org (hired in the last 24 months). Has a tiny team. Needs a tool that makes them credible to the audit committee.
- Director of ESG or Head of ESG Reporting. Sits in legal, IR, or finance. Cares about defensibility and auditor sign-off more than dashboards.
- Controller or Assistant Controller, when sustainability reporting has been absorbed into the financial close calendar (increasingly common under CSRD because ESRS data is audited).

Common buyer profile: $100M to $2B revenue, 1-3 person sustainability function, no in-house carbon-accounting PhD, has an external consultant on retainer (often a Big Four practice or a boutique) and wants to bring more of the work in-house to cut consulting spend.

Budget authority sits with the CFO or audit committee. The CSO recommends, finance signs.

Pains we have heard repeatedly in discovery:

- "We pay our consultant $300K a year to maintain a spreadsheet. I cannot defend that to my CFO for a third year."
- "Watershed quoted us $450K plus implementation. We are a $600M company. That math does not work."
- "The auditor asked where a number came from and we could not answer in under three days."
- "We are about to be in scope for CSRD wave 2 and nobody internally has done this before."

The buyer wants leverage, not magic. They want to do the work themselves, faster, with a tool an auditor will respect.

## User Personas

The buyer is not always the user. Daily usage skews to three roles.

- Sustainability Analyst. Day-to-day operator. Chases utility bills, reconciles activity data, runs the calculations, drafts the disclosure. Often two to four years out of school. Has used a spreadsheet-and-consultant stack and is frustrated by it. Wants speed and traceability.
- Finance Analyst (FP&A or Controller's team). Maps GL spend data to emission categories for Scope 3 spend-based estimates. Cares about reconciliation, period close, and not breaking the financial audit.
- Supply Chain Manager. Owns vendor data collection for Scope 3 Category 1 (Purchased Goods and Services) when the company moves from spend-based to supplier-specific data. Needs lightweight vendor outreach tooling.

Secondary users: external auditor (read-only, time-boxed access during assurance engagement), external consultant (collaborator seat).

Anti-persona: the Fortune 50 enterprise sustainability team with 30 headcount, a custom data lake, and a multi-million-dollar Watershed or Persefoni deployment. We are not selling to them. If we win them later it is a happy accident, not the plan.

## Capability Surface

Version 1 scope.

- Activity-data ingest. Utility bill ingest via direct API (Urjanet, Arcadia, or equivalent), OCR fallback for PDF bills, fuel records (CSV and API), vehicle telemetry (Geotab, Samsara connectors), ERP spend data pull (NetSuite, SAP, Microsoft Dynamics, QuickBooks) for Scope 3 spend-based calculations.
- Emission-factor library. Curated and versioned. EPA eGRID (US grid), IEA (international grid), UK DEFRA, regional factors (AIB residual mix for EU market-based, state-level US factors). Every factor carries provenance: source, vintage, geography, methodology notes.
- Calculation engine. Scope 1 (stationary combustion, mobile combustion, fugitive, process). Scope 2 (location-based and market-based, dual reporting per GHG Protocol Scope 2 Guidance). Scope 3 v1 categories: Category 1 (Purchased Goods and Services), Category 6 (Business Travel), Category 7 (Employee Commuting), Category 11 (Use of Sold Products). Other Scope 3 categories on roadmap.
- Methodology audit log. Every calculated number traces back to: input activity data, emission factor used, factor vintage, factor source, calculation formula, who ran it, when. Immutable log per reporting period.
- Disclosure-ready exports. CDP Climate Change questionnaire pre-fill, CSRD ESRS E1 datapoint export, California SB 253 report format, SBTi inventory submission format, generic GHG Protocol corporate inventory PDF.
- Year-over-year comparison and restatement. When methodology improves or a factor library updates, prior-year inventories can be restated with full disclosure of what changed and why. Original and restated values both preserved.
- Auditor view. Time-boxed external auditor seat. Read-only access to the methodology audit log, source documents, factor provenance, and calculation lineage. Export pack designed against ISAE 3000 / AT-C 105 limited assurance workpaper expectations.

## Methodology Framing

We follow the GHG Protocol Corporate Standard and the Scope 2 Guidance. We are explicit about choices.

- Scope 1: direct emissions from owned and controlled sources. Includes stationary combustion (boilers, furnaces), mobile combustion (owned fleet), fugitive emissions (refrigerants), and process emissions where applicable.
- Scope 2: indirect emissions from purchased electricity, steam, heating, and cooling. We report both location-based (grid average) and market-based (contractual instruments such as RECs, GOs, PPAs, supplier-specific factors). Dual reporting is mandatory under the Scope 2 Guidance and we will not let a customer skip one of them.
- Scope 3: 15 categories under the GHG Protocol Corporate Value Chain Standard. We support Categories 1, 6, 7, and 11 in v1. We are honest with customers that Scope 3 estimates carry wider uncertainty than Scope 1 and 2, and we surface that uncertainty in the output.
- Spend-based vs activity-based vs supplier-specific Scope 3. We support all three tiers and let the customer move up the data-quality ladder over time. The methodology audit log records which tier was used for each calculation.
- Biogenic emissions are tracked separately, not netted into the Scope 1 total, consistent with GHG Protocol guidance.

We do not use a black-box AI estimator for emission factors. If a number is in the inventory, a human can trace it to a published source. AI is used in the product (OCR, classification, anomaly flagging on activity data), but not as a source of emission factors or calculation logic.

Where the GHG Protocol leaves a choice to the reporter (consolidation approach: operational control vs financial control vs equity share, biogenic carbon treatment, intensity metric selection), Tidewater forces an explicit, documented decision and carries it through the methodology audit log.

## Data Input and Integration Model

Three input tiers, in order of preference.

1. Direct API integration. Utility providers via aggregator (Urjanet, Arcadia), fleet telematics (Geotab, Samsara), travel (Concur, Navan, TripActions), ERP (NetSuite, SAP S/4HANA, Dynamics 365, QuickBooks Online).
2. Structured file upload. CSV and Excel templates for activity data the APIs do not cover. Validated on ingest, with row-level error reporting.
3. Document upload with OCR. PDF utility bills, fuel receipts, refrigerant service records. Extracted fields go to a review queue, not directly into the calc.

Output of every ingest path is a normalized activity record with: source document reference, facility, period, quantity, unit, fuel or energy type, ingest method, confidence flag.

Facility model: customer-defined hierarchy of legal entities, sites, and emission sources, with effective-dated changes (acquisitions, divestitures, closures) so prior-year inventories remain accurate after corporate structure changes.

Unit handling: all activity data normalized to a canonical unit per fuel or energy type on ingest. Unit conversion factors are themselves versioned and traceable. Yes, this matters. We have seen multi-percent inventory swings caused by a customer mixing kWh and MWh in a spreadsheet.

Data residency: EU customer data is stored and processed in an EU region (Frankfurt or Ireland). US customers default to US region. This is hard-partitioned, not a config toggle.

## Audit and Assurance

Under CSRD, sustainability disclosures require limited assurance starting day one and reasonable assurance on a multi-year transition. California SB 253 requires limited assurance on Scopes 1 and 2 starting 2026 reports, with Scope 3 limited assurance phasing in. Voluntary CDP submissions are not assured but increasingly are.

V1 design target: limited assurance defensibility under ISAE 3000 (international) and AT-C 105 (US).

What that means concretely:

- Every number in the disclosure ties to a calculation, which ties to an emission factor (with vintage and source) and an activity-data record (with source document).
- The methodology audit log is immutable for closed reporting periods. Reopening a period creates a restatement, not an overwrite.
- Auditor view exports a workpaper-style pack: inventory summary, methodology document, factor library snapshot for the period, calculation samples, sampling support for activity data, list of estimations and their basis.
- Segregation of duties: data entry, review, and lock-period roles are separable.

Reasonable assurance support is on the roadmap. It requires deeper sampling, evidence-management features, and tighter controls. We do not claim it in v1.

We have engaged a Big Four climate-assurance partner as a design reviewer (not a paid consultant, an advisory relationship). Their feedback drives the auditor-view spec. Specific items they have flagged as deal-breakers if missing: factor library version pinning per reporting period, ability to reproduce a prior calculation exactly six months later, evidence retention beyond the active subscription period (cold storage for closed periods).

Data quality is its own surface. Every activity-data record carries a data-quality tag (primary, secondary, proxy, estimated) and a confidence flag. Inventory output reports a data-quality score per scope and per Scope 3 category. This is not cosmetic. It is what an auditor opens first.

## Constraints

- Methodology transparency is non-negotiable. No black-box calculation paths. Every number explainable.
- Audit-ready output for limited assurance is a v1 requirement, not a v2 feature.
- Data residency must be honored at the infrastructure layer. EU data does not leave the EU region.
- Customer team size assumption: 1 to 3 person sustainability function. The product must be operable without a dedicated technical admin.
- Calculation engine must support restatement. Prior years are not immutable when methodology improves; they are versioned.
- Mid-market price point: target ACR $60K to $180K. The product cannot require a six-figure implementation services engagement to stand up.
- Time-to-first-locked-inventory: under 90 days from contract for a customer with reasonable activity-data hygiene. This is a product constraint, not a services promise.
- Factor library updates: at least quarterly for major sources (eGRID, IEA, DEFRA), with full changelog and impact assessment on customer inventories.
- Single-tenant data isolation by default. Multi-tenant infra is fine, but no cross-customer data inference, no aggregated benchmarks that leak any single customer's data, no model training on customer activity data.

## Non-Goals

- Not a carbon-offset or carbon-credit marketplace. We do not transact credits.
- Not a Scope 4 (avoided emissions) tool. The methodology is contested and not assurance-ready. Out of v1.
- Not a net-zero target-setting or decarbonization-strategy consulting product. SaaS only. Consulting flows through partner channel.
- Not a financed-emissions module for banks and asset managers. That is PCAF territory and a separate vertical with separate buyers.
- Not a product-level LCA (life-cycle assessment) tool. Corporate inventory only. Product LCA is a different category (Sphera, ecoinvent-based tools, Makersite).
- Not consumer-facing emissions tracking. B2B only.
- Not a generic ESG platform. Climate (E1) only in v1. Social and governance disclosures are out of scope.
- Not a TCFD-style scenario analysis tool. Physical and transition risk scenario modeling is its own product surface (Jupiter, Cervest, Sust Global occupy it). Out of v1.
- Not a sustainability strategy or marketing-claim tool. We do not generate sustainability report narratives or greenwashing-adjacent collateral. The output is data and disclosure-ready exports; the customer writes their own story.

## Success Signal

We will know this is working when the following three signals land together.

- ARR. Year 1 target $4M to $6M ARR from 30 to 60 paying customers in the design-partner and first-cohort phase.
- Disclosure-cycle retention. The real loyalty test is whether customers renew after submitting their first CSRD, SB 253, or CDP report through Tidewater. Target gross retention above 90 percent through the first full disclosure cycle.
- Audit defensibility. Zero auditor qualifications or adverse findings in the first 12 months of customer use that are attributable to Tidewater methodology, factor library, or calculation engine. Customer-side data quality issues do not count against this; tool-caused issues do.

Leading indicators we will watch: time from contract signature to first locked inventory period, percentage of Scope 2 customers using market-based reporting correctly, percentage of Scope 3 spend-based customers who graduate to activity-based within 18 months.

Anti-signals we are watching for, which would tell us we are building the wrong thing: heavy reliance on professional services revenue to stand up customers (anything over 25 percent of bookings is a warning), customers asking us to "just give them a number" instead of engaging with the methodology, churn concentrated immediately after first disclosure submission, auditors recommending customers switch off Tidewater for assurance defensibility.

Reference customer profile for case studies: one CSRD wave 2 customer (EU-headquartered manufacturer, $400M to $1B revenue), one California SB 253 customer (US-headquartered, $1B to $2B revenue, multi-state operations), one voluntary CDP customer with no regulatory obligation yet (proves the value proposition outside compliance pressure).

## Open Questions

- SEC climate rule. If it returns in a modified form in 2026 or 2027, do we adjust the disclosure-export module, or wait for a final rule? Current bias: wait, because building against draft text has burned this category before.
- Reasonable assurance timing. CSRD transitions from limited to reasonable assurance over several years. When do we commit to the reasonable assurance feature set? Tied to design-partner appetite.
- Scope 3 category expansion order. After Categories 1, 6, 7, 11 in v1, the next most-requested are Category 4 (Upstream Transportation), Category 5 (Waste), and Category 12 (End-of-Life Treatment). Order is open and customer-driven.
- Emission factor library: build vs license. Curating our own factor library is differentiating but expensive to maintain. Do we license a commercial library (e.g., Climatiq, ecoinvent for spend-based proxies) and layer curation on top, or fully self-curate?
- Partner channel for consulting. Big Four practices and boutique consultancies are both an acquisition channel and a competitive threat (they have their own tools). What is the partner program shape?
- Pricing model. Flat-fee SaaS by revenue band, per-facility, per-Scope-3-category, or hybrid? Mid-market sensitivity is real.
- Localization. CSRD reporting is in English in practice for the ESRS dataset, but local-language disclosure narratives matter. How much UI localization is in v1?
- Supplier engagement module. Scope 3 Category 1 supplier-specific data collection is the highest-value Scope 3 work but the heaviest product surface. Bundled in v1 or a v1.5 add-on?
- Competitive positioning against Watershed, Persefoni, and Sweep. They are moving down-market as enterprise saturates. How long do we have before they price-compress into our segment, and what is our durable wedge (methodology transparency, mid-market UX, price) when they do?
- AI feature posture. Customers want AI in the product (bill OCR, classification, anomaly detection). They do not want AI making up numbers. Where exactly is the line we will not cross, and how do we communicate it without sounding anti-AI?
- Open-source factor library. Do we publish our curated factor library (with provenance) as open data? Argument for: trust, marketing, auditor confidence. Argument against: it is the most expensive thing we maintain.
