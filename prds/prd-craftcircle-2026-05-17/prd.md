# CraftCircle — Product Requirements Document

**Document Owner:** Marketplace Product Team
**Status:** Draft v0.9 — Pre-Beta Lock
**Last Updated:** 2026-05-17
**Reviewers:** Trust & Safety, Payments, Shipping Ops, Maker Success, Buyer Growth, Legal, Design

---

## 1. Vision

CraftCircle is the home for makers who still touch every piece they sell, and for buyers who can tell the difference. We are building the premium, curated alternative to the mass-market handmade marketplaces that have lost the plot. Where the incumbents drifted toward drop-shipped jewelry and AI-generated wall art masquerading as handmade, CraftCircle holds a line: every listing comes from a verified human maker, and every transaction carries provenance from the bench to the buyer's door.

We believe handmade is a relationship, not a SKU. A buyer should be able to see the workshop, hear the maker's voice, ask about the wood grain, and know that the ceramic mug arriving Tuesday was thrown on a wheel by a specific person in a specific town. We believe makers building real businesses deserve tooling that respects the craft — clean storefronts, fair fees, predictable payouts, and dispute resolution that does not treat them like interchangeable inventory.

CraftCircle is a curated circle, not an open bazaar. Makers apply. Listings are reviewed. Storefronts get the design polish of a boutique brand. Buyers get a discovery experience that feels closer to a gallery walk than an infinite-scroll bargain bin. The promise is simple: if you find it on CraftCircle, a human made it, and that human is still the one who ships it.

Our north star is a thriving middle class of professional makers — artisans clearing six-figure GMV from their craft, sustained by buyers who would rather own one beautiful thing than ten disposable ones. We win when a maker quits their day job, when a buyer stops apologizing for spending real money on real objects, and when "I got it on CraftCircle" becomes shorthand for taste.

---

## 2. Problem

### 2.1 The handmade marketplace has been polluted

The dominant handmade marketplace — Etsy — opened its gates to mass producers, drop-shippers, and resellers years ago, and the consequences are visible on every search result page. A buyer looking for a handmade leather wallet wades through dozens of identical-looking SKUs sourced from the same overseas factories, all tagged "handmade." Search ranking favors volume sellers and ad spend, not craft. Real makers compete on price with operations that have nothing in common with their workshop.

### 2.2 Buyers cannot tell what is real

The signal-to-noise ratio for buyers seeking authentic handmade goods has collapsed. Listing photography is staged identically across competing sellers. "Personalization" often means a laser engraving station, not a human craftsperson. Buyers who want to give a meaningful, made-by-someone gift end up either overpaying at galleries or rolling the dice and feeling cheated when the package arrives looking like Amazon overflow.

### 2.3 Makers want better tools, fairer economics, and respect

Established makers we have interviewed consistently report:

- **Fee compression:** Total take rate (listing + transaction + payment + ads + offsite ads) has crept past 20% on incumbent platforms for many sellers.
- **Discovery roulette:** Algorithm changes erase months of revenue overnight with no recourse.
- **Tooling stagnation:** Storefront customization is cosmetic. Order management has not meaningfully evolved. Reviews favor buyers in disputes by default.
- **Dignity:** Makers want to be treated as small businesses, not as inventory rows.

### 2.4 The opportunity

A curated, premium, maker-first marketplace with rigorous verification, transparent economics, modern storefront tooling, and a discovery experience built for taste rather than volume can win the top decile of makers and the buyers who follow them. CraftCircle is that marketplace.

---

## 3. Target Audiences

### 3.1 Makers

Our primary maker target is the **established professional artisan** — someone with an existing craft business, a portfolio of work, and the operational maturity to fulfill orders consistently. Specifically:

- 2+ years of active selling on at least one platform or direct-to-consumer channel
- Capacity to produce repeatedly (not one-off hobbyists)
- Revenue trajectory plausibly reaching $100K+ annual GMV on CraftCircle within 24 months
- Categories at launch: fine jewelry, ceramics & pottery, textiles & fiber arts, woodwork & furniture, leathercraft

Secondary: **rising makers** — under 2 years of selling but with distinctive work, strong photography, and clear voice. Admitted selectively to keep the curation bar visible.

### 3.2 Buyers

Primary buyer target: **gift-givers and home decor enthusiasts aged 28–55**, household income $100K+, located in major US metros at launch with international expansion to follow. Psychographics matter more than demographics: these are buyers who read the artist statement before they read the price tag, who would rather own one well-made object than three forgettable ones, and who experience a small thrill when they can tell the story of where something came from.

Segment splits we will track:

- **Gift-givers** (~40% of GMV target): occasion-driven, higher AOV, sensitive to ship-by dates
- **Home decor enthusiasts** (~35%): collection behavior, return visits, ceramics & textiles heavy
- **Collectors** (~15%): high LTV, follow specific makers, drive commission work
- **Self-gifters / treat purchases** (~10%): impulse-driven, jewelry heavy

---

## 4. Personas

### 4.1 Nora — New Maker

Nora is 31, a ceramicist in Asheville who has been selling at local markets and on Instagram for 18 months. She has roughly 4,000 followers, a working website that takes Stripe payments, and a kiln in her garage. She has avoided Etsy because her friends who sell there complain constantly. She heard about CraftCircle from another maker. She wants a storefront that looks like a real brand, not a generic template, and she needs to learn marketplace mechanics without getting buried in dashboards.

**Goals:** Replace 30% of her income, then 60%, then quit her part-time job. Build a recognizable brand. Sell commissions for higher-end pieces.

**Frustrations:** Hates writing product copy. Cannot afford a photographer. Worries her work is "not premium enough" to be accepted.

### 4.2 Marcus — Established Maker

Marcus is 47, a leatherworker in Portland with a 6-person studio doing $480K in annual GMV across Etsy, his own Shopify, and wholesale. He is exhausted by Etsy's fee creep and arbitrary policy enforcement. He wants a second growth channel that respects his margin and his time.

**Goals:** Move 30%+ of DTC volume to CraftCircle within 12 months. Run two studio livestreams per month. Reduce time spent on customer service per order.

**Frustrations:** Fee structures that nickel-and-dime. Platforms that let counterfeit listings of his work persist for weeks. Dispute systems that side with buyers reflexively.

### 4.3 Priya — Gift-Buyer

Priya is 38, a marketing director in Brooklyn. She buys 15–25 gifts per year — anniversaries, hostess gifts, baby showers, the works — and her standing rule is "no Amazon for anyone I actually like." She values story and provenance and is happy to spend $80–$300 per gift if she trusts what she is buying.

**Goals:** Find distinctive, ship-on-time gifts with confidence. Save makers she likes for return visits. Read the maker's story without it feeling like marketing.

**Frustrations:** Search results that look identical across sellers. Late shipping that ruins the occasion. Reviews she does not trust.

### 4.4 David — Collector

David is 54, a software executive in Austin. He collects studio ceramics — specifically wood-fired pieces. He follows about 30 makers across Instagram and various platforms and commissions 4–8 pieces per year, typically $500–$2,500.

**Goals:** Direct relationships with makers. Get first look at new drops. Commission custom work with clear scope and payment milestones.

**Frustrations:** Makers' DMs going unread. No way to track commission progress. Escrow uncertainty on high-value custom work.

### 4.5 Renée — Customer Support Agent

Renée is 29, an experienced trust-and-safety operator who joined CraftCircle in month 4 of beta. She handles disputes, fraud reviews, and counterfeit reports. She works from a queued workbench and needs full transactional and conversational context per case.

**Goals:** Resolve disputes in under 48 hours median. Keep maker-side appeals overturn rate above industry benchmarks. Identify fraud rings before payout.

**Frustrations:** Context-switching across tools. Cases where evidence is buyer-text-only with no photos. Makers who go dark mid-dispute.

---

## 5. User Journeys

### 5.1 Maker Onboarding (Marcus — coming from Etsy and Shopify)

1. **Discovery:** Marcus hears about CraftCircle from a podcast interview with another leatherworker. He visits the landing page and reads the maker pitch.
2. **Application:** He clicks "Apply to Sell" and completes a 12-field application: legal name, business name, primary craft, years selling, current channels, links to existing storefronts, three portfolio photos, a 200-word artisan statement, US tax info, and references to verify human authorship.
3. **Verification:** Within 5 business days, a maker success reviewer evaluates portfolio authenticity, cross-checks existing channels for consistency, and runs reverse-image search to flag drop-ship signatures. Marcus is approved.
4. **Storefront setup:** Marcus is dropped into the storefront editor with a guided checklist (brand colors, hero image, about section, shipping profiles, payout method). He imports 18 existing listings from a Shopify CSV.
5. **Listing review:** Each imported listing is queued for category review. 16 pass automatically; 2 are flagged for clarification on materials sourcing. Marcus replies in-thread and they clear within 24 hours.
6. **First payout setup:** Stripe Connect Express onboarding, ACH verified, payout cadence selected (weekly).
7. **Soft launch:** Marcus's storefront goes live. He is added to the "New to CraftCircle" curated row for 14 days. He records a 90-second studio video that is featured on his store page.
8. **First sale within 6 days.** Marcus rates the onboarding 9/10, flags one bug in the CSV importer.

### 5.2 Buyer Custom Commission (David commissioning a wood-fired vase)

1. **Discovery:** David follows maker Lin on CraftCircle. He sees a "Commissions Open" badge on her storefront.
2. **Inquiry:** He opens the commission request form: budget range, deadline, references, written brief. Submits.
3. **Maker proposal:** Lin responds within 48 hours with three concept directions, a firm price ($1,800), a 4-week timeline, and a milestone breakdown: 30% deposit, 40% at bisque, 30% at completion.
4. **Escrow setup:** David accepts. CraftCircle escrows the deposit. Lin sees deposit-cleared status and begins work.
5. **In-progress updates:** Lin posts two progress photos to the commission thread at week 1 and week 2. The second milestone unlocks the next 40% from David's pre-authorized total.
6. **Completion:** Lin marks the piece complete and uploads finished photography. David approves; final 30% releases. Shipping label generated.
7. **Delivery:** Vase arrives intact, white-glove packaging. David reviews 5 stars, attaches photos to the maker's portfolio (with permission).

### 5.3 Damaged Shipment Dispute (Priya's gift arrives broken)

1. **Receipt:** Priya receives a $145 ceramic vase 11 days after ordering, two days before the gift occasion. It is shattered in the box.
2. **Report:** From the order page she clicks "Report a Problem" → "Arrived damaged." She uploads four photos including the box and packaging.
3. **Auto-triage:** The system holds the relevant escrow funds, opens a case, and notifies the maker with a 72-hour first-response SLA.
4. **Maker response:** The maker confirms the piece was shipped with appropriate packaging and offers a replacement on a 2-week timeline or a full refund. The maker's prior damage rate is checked: it is below platform average. No fraud signal.
5. **Buyer decision:** Priya accepts the refund (the gift date has passed). Funds release within 1 business day.
6. **Carrier claim:** CraftCircle's shipping integration auto-files a damage claim with the carrier on behalf of the maker. The maker is made whole regardless of carrier outcome (within platform policy thresholds).
7. **Resolution:** Renée closes the case. Maker rating impact is zero (damage-in-transit is excluded from quality scoring). Priya receives a "we are sorry" credit toward a future order.

---

## 6. Marketplace Mechanics

### 6.1 Commission Structure

- **Transaction fee:** 7% of item subtotal (positioned below Etsy's effective blended rate)
- **Payment processing:** 2.9% + $0.30 per transaction (pass-through Stripe)
- **Listing fee:** $0.00 (no per-listing fees at launch)
- **Promoted placement (optional):** auction-based, capped at 15% of impressions per category to avoid Etsy-style ads pollution
- **Commission/custom work surcharge:** 0% additional — same 7% applies
- **Live video sales:** 7% transaction fee + 1% live-shopping surcharge to fund streaming infrastructure

### 6.2 Payout Schedule

- **Standard cadence:** Weekly, every Tuesday
- **Optional faster cadence:** Daily payouts available after 90 days and $5K cleared GMV
- **First payout hold:** New makers' first payout held 14 days after first sale clears

### 6.3 Escrow Hold Periods

- **Standard orders:** Funds release to maker 3 days after delivery confirmation
- **High-value orders ($500+):** 7 days after delivery confirmation
- **Commissioned work:** Milestone-based release per maker/buyer agreement
- **Buyer-initiated returns window:** 14 days from delivery

### 6.4 Fraud Holds

- **New maker first 30 days:** All payouts held until tracking shows delivered
- **Velocity flag:** Sudden order spike >5x trailing 30-day average triggers manual review
- **Geo mismatch flag:** Buyer billing/shipping/IP discrepancies route to fraud queue
- **Chargeback history:** Makers with chargeback rate >1% enter enhanced review

---

## 7. Functional Requirements

### 7.1 Maker Onboarding

- **FR-1:** The platform shall provide a multi-step application flow capturing business identity, craft category, portfolio samples, and references.
- **FR-2:** The platform shall support reviewer-assisted approval with documented rubric scoring (craft authenticity, photo quality, brand cohesion, channel consistency).
- **FR-3:** The platform shall support reverse-image search against known drop-ship catalogs during application review.
- **FR-4:** The platform shall integrate Stripe Connect Express for maker payout onboarding.

### 7.2 Storefront Editor

- **FR-5:** Makers shall configure storefront branding including hero image, color palette (constrained to accessible-contrast options), about section, and studio video.
- **FR-6:** The storefront editor shall offer 4 layout templates with consistent typographic system, no fully custom CSS at launch.
- **FR-7:** Makers shall pin up to 6 featured products to the top of their storefront.
- **FR-8:** Storefronts shall expose a public URL of the form `craftcircle.com/m/<handle>`.

### 7.3 Listing Management

- **FR-9:** Makers shall create listings with title, description, up to 10 photos, 1 video, dimensions, materials, price, inventory count, shipping profile, and processing time.
- **FR-10:** Listings shall require a materials disclosure section with a structured field for primary materials.
- **FR-11:** The platform shall support CSV import from Etsy and Shopify with field mapping preview.
- **FR-12:** Listings shall enter a moderation queue on first submission; subsequent edits to approved listings publish immediately unless flagged.

### 7.4 Order Management

- **FR-13:** Makers shall view orders in a unified queue with statuses: new, in production, ready to ship, shipped, delivered, completed, disputed, refunded.
- **FR-14:** Makers shall mark processing milestones for commissions tied to escrow release events.
- **FR-15:** The platform shall send buyer-facing status notifications on every state transition via email and (if opted in) push.

### 7.5 Payment & Payout

- **FR-16:** The platform shall process payments through a PCI DSS Level 1 compliant provider (Stripe at launch).
- **FR-17:** Buyers shall pay with major credit cards, Apple Pay, Google Pay, and (US) ACH for orders over $500.
- **FR-18:** Makers shall view a payout dashboard showing pending balance, in-escrow balance, next payout date, and historical payouts with downloadable statements.

### 7.6 Shipping Integration

- **FR-19:** The platform shall integrate USPS, UPS, and FedEx for label purchase, tracking, and rate shopping.
- **FR-20:** Makers shall optionally select "ship direct" — using their own carrier account or method — while still providing tracking input for buyer visibility and escrow logic.
- **FR-21:** The platform shall auto-file carrier damage claims when a delivered order opens a damage dispute with photo evidence.

### 7.7 Reviews & Ratings

- **FR-22:** Buyers shall submit a review only after delivery confirmation, with 1–5 star rating, written review, and optional photo attachments.
- **FR-23:** Reviews involving disputes shall be flagged and reviewed before public display when the dispute outcome materially conflicts with the review claim.
- **FR-24:** Makers shall publicly respond to any review once, with edits permitted within 7 days of posting the response.

### 7.8 Dispute Resolution

- **FR-25:** Buyers shall open disputes in categories: not received, arrived damaged, not as described, wrong item, unauthorized commission deviation. Each category has a structured evidence prompt.
- **FR-26:** The dispute system shall enforce SLA timers: maker first response 72 hours, buyer reply 72 hours, support escalation after 7 days of inactivity.

### 7.9 Search & Discovery

- **FR-27:** The platform shall provide faceted search across category, materials, price band, maker location, and processing time, with relevance ranking that does not weight ad spend above 15% of any result page.
- **FR-28:** The platform shall surface editorially curated collections refreshed at least weekly.

### 7.10 Personalization

- **FR-29:** Buyers shall follow makers, save items, and receive a personalized "for you" row tuned on browse + purchase + follow signals.

### 7.11 Messaging

- **FR-30:** Buyers and makers shall message within the platform with attachment support, with all commission-related threads preserved in the order record.

### 7.12 Mobile App Parity

- Mobile apps (iOS, Android) shall reach parity with web for buyer browsing, purchase, messaging, and order tracking by GA. Maker tools on mobile shall cover order management, messaging, and basic listing edits at GA; full listing creation deferred to post-GA.

### 7.13 Admin Tools

- Internal admin tooling shall cover maker approval workflow, listing moderation queue, dispute workbench, fraud review queue, financial ledger inspection, and editorial collection builder.

---

## 8. Non-Functional Requirements

- **NFR-1 (PCI DSS):** All payment surfaces shall be PCI DSS Level 1 compliant; card data never touches CraftCircle servers.
- **NFR-2 (GDPR/CCPA):** Buyer and maker data handling shall comply with GDPR and CCPA, including data export, deletion, and consent flows.
- **NFR-3 (Search latency):** Search result pages shall return within latency comparable to peer marketplaces; specific p50/p95 targets are set in the engineering spec.
- **NFR-4 (Mobile-first):** Buyer-facing web shall be mobile-first responsive; mobile shall account for at least 60% of buyer traffic in the architecture's capacity planning.
- **NFR-5 (Internationalization):** Platform shall support six markets at international launch (US, CA, UK, AU, DE, FR), including localized currency, tax handling, and language for buyer-facing surfaces.
- **NFR-6 (Availability):** Platform shall target 99.9% availability for core buyer flows (browse, purchase, order tracking).
- **NFR-7 (Accessibility):** Buyer-facing surfaces shall meet WCAG 2.1 AA.
- **NFR-8 (Audit logging):** All financial events shall be immutably logged for regulatory and dispute review.

---

## 9. Trust & Safety

### 9.1 Counterfeit Detection

- Maker-submitted "original work" attestation on every listing
- Reverse-image search against known counterfeit and drop-ship catalogs at listing submission and on a rolling re-scan cadence
- Maker-reported counterfeit complaints route to a dedicated queue with 24-hour first-response SLA
- Repeat counterfeit offenders banned permanently with payout clawback within policy bounds

### 9.2 Mass-Producer Detection Signals

- Listing velocity beyond plausible solo or small-studio production capacity
- Photographic signatures matching factory-style catalogs
- Materials disclosure inconsistent with claimed craft
- Pricing patterns inconsistent with handmade economics (e.g., $4 silver rings)
- Shipping origin clusters with known drop-ship fulfillment centers
- Customer service language signatures matching templated reseller scripts

### 9.3 Maker Verification

- Identity verification via Stripe Connect KYC
- Portfolio review by maker success team during application
- Optional studio video as part of application (encouraged, not required)
- Annual re-verification ping for active makers
- Random sampled "show your bench" requests for makers exhibiting risk signals

---

## 10. Risk Register

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| Counterfeit listings slip through onboarding | Medium | High | Layered review, rolling rescans, maker reporting tools, fast-takedown SLA |
| Payment fraud rings target new-maker payout hold gaps | Medium | High | Velocity flags, geo flags, ML risk scoring at checkout, payout holds on suspicious patterns |
| Shipping dispute volume scales past support capacity | High | Medium | Auto-triage, structured evidence intake, carrier auto-claim filing, tiered SLA queues, surge staffing playbook |
| Maker concentration risk — top 1% of makers drive disproportionate GMV | Medium | Medium | Curated discovery rows promoting mid-tier makers, category caps on featured placement, maker success investment in tier 2 makers |
| Etsy retaliatory pricing or policy changes targeting overlap sellers | Medium | Medium | Exclusive maker partnerships, differentiated brand positioning, faster payouts |
| Regulatory shifts in marketplace facilitator tax | Medium | Medium | Tax engine vendor, legal monitoring, modular tax handling per jurisdiction |
| Counterfeit takedown disputes invite legal exposure | Low | High | Documented evidence standards, appeal process, counsel-reviewed policies |
| Maker churn after fee changes | Medium | High | Telegraph any fee changes 90+ days in advance, grandfather where possible |

---

## 11. Milestones

### 11.1 Closed Beta (Q3 2026)

- 200 makers onboarded across all five launch categories
- Invite-only buyer cohort of ~5,000
- Core buyer flows functional: browse, purchase, message, review, dispute
- Core maker flows functional: storefront, listings, orders, payouts
- Manual operations acceptable for moderation and disputes

### 11.2 Public Launch (Q4 2026)

- Open buyer registration with curated maker waitlist
- Editorial discovery surfaces live and refreshed weekly
- Carrier integrations fully live (USPS, UPS, FedEx, ship-direct option)
- Live video shopping in beta for invited makers
- Target: 1,500 makers, paid acquisition begins

### 11.3 Mobile App (Q1 2027)

- iOS and Android apps with buyer parity
- Maker mobile order management and messaging
- Push notifications for order status, messages, and editorial drops

### 11.4 International Expansion (Q3 2027)

- Six-market launch: US, CA, UK, AU, DE, FR
- Localized currency, tax, language for buyer surfaces
- International maker onboarding with region-appropriate KYC and tax handling

---

## 12. Non-Goals

CraftCircle is explicitly **not**:

- **A digital goods marketplace.** No SVG cut files, no printable wall art, no Lightroom presets. Physical handmade goods only.
- **An open marketplace for mass producers.** No drop-ship operators, no factory-sourced inventory, no resellers, no print-on-demand catalogs.
- **A logistics network.** We integrate with carriers; we do not run warehouses, fulfillment centers, or owned delivery fleets.
- **A same-day delivery service.** Handmade has a cadence. We design buyer expectations around it.
- **A vintage marketplace at launch.** Vintage and antique categories may be considered post-international expansion; not in scope through Q3 2027.
- **A wholesale platform.** B2B maker-to-retailer sales are out of scope at launch.

---

## 13. Success Metrics

We will know CraftCircle is working when we see:

- **Strong maker satisfaction** across the maker base, particularly among established artisans.
- **Healthy GMV growth** quarter over quarter through and beyond public launch.
- **Delight buyers** with a discovery and purchase experience that feels distinctly better than peer marketplaces.
- **Good engagement** with maker storefronts, video shopping, and curated collections.
- **Reasonable dispute outcomes** that protect both sides and feel fair.
- **A vibrant community** where makers and buyers form real relationships.
- **Brand resonance** such that CraftCircle becomes shorthand for taste in handmade.

Detailed quantitative targets, instrumentation, and tracking cadence are owned by the analytics workstream and will be attached as an appendix once the beta dataset matures.

---

## 14. Open Questions

- Should commission escrow milestones be platform-defined templates or fully free-form per maker?
- What is the right policy for maker-to-maker collaborations (e.g., a jeweler setting another maker's stones)?
- How aggressively should we curate against "premium-priced but uninteresting" work that technically meets handmade criteria?
- Live video shopping: in-house infrastructure vs. third-party platform integration at launch?
- International tax handling: build vs. buy decision pending legal review.

---

## 15. Appendix

### 15.1 Glossary

- **GMV:** Gross Merchandise Value — total value of goods sold through the platform before fees.
- **AOV:** Average Order Value.
- **Escrow:** Funds held by the platform between buyer payment and maker payout, conditional on delivery and dispute resolution windows.
- **Ship-direct:** Maker option to fulfill shipping outside platform-purchased labels while still providing tracking.
- **Curated discovery:** Editorially selected collections shown on the homepage and category pages.

### 15.2 Document Change Log

| Date | Version | Author | Notes |
|---|---|---|---|
| 2026-04-22 | v0.1 | Marketplace PM | Initial draft from vision workshop |
| 2026-05-02 | v0.5 | Marketplace PM | Persona pass, FR draft |
| 2026-05-12 | v0.8 | Marketplace PM | T&S section, risk register |
| 2026-05-17 | v0.9 | Marketplace PM | Pre-beta lock draft for review |
