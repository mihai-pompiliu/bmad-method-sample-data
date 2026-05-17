# PRD: PalletPulse — Industrial IoT Pallet Tracking System

**Version:** 1.0
**Date:** 2026-05-17
**Owner:** Product (IoT Tracking)
**Status:** Draft for engineering review
**Customer of Record:** Tier-1 3PL, ~50,000 pallets, 14 warehouses (US: 9, EU: 5)

---

## 0. Executive Summary

PalletPulse is a purpose-built IoT telemetry layer for pooled wooden shipping pallets. A low-cost LoRaWAN sensor (sub-$18 BOM, 5-year battery life) attaches to each pallet stringer and reports location, ambient temperature, and impact events to a multi-region backend. The anchor customer is a Tier-1 3PL with 50,000 pallets across 14 warehouses in North America and Europe. The system replaces RFID dock portals plus manual handheld scanning, which together leak ~$3.2M/year in pallet loss and miss cold-chain excursions on pharma shipments. Hardware is the binding constraint: every product decision in this document is calibrated against battery life, EU868 duty cycle, BOM cost, and the operational reality that 1,000 sensors per year will need physical replacement in the field.

## 1. Problem

Our reference 3PL customer operates a circulating pool of approximately 50,000 wooden GMA / EUR pallets across 14 distribution centers in North America and Europe. The current tracking stack is RFID portals at dock doors plus manual handheld scanning at putaway and pick. This stack has aged into a real operational liability:

- **Pallet loss rate is 6.8% annually**, against an industry benchmark of 3–4%. At a blended replacement + downstream-disruption cost of ~$95/pallet, that is ~$3.2M/yr in pure leakage. The customer believes a meaningful fraction is theft and "permanent borrows" by trading partners, but they cannot prove it without location evidence.
- **Mean time to recover a missing pallet is 11 days.** Most recovery happens by accident (a pallet shows up at a sister DC weeks later). There is no proactive recovery workflow because there is no live location signal.
- **Manual scan error rate is 2.3%** on inbound and 3.1% on outbound. Operators routinely scan the wrong pallet, miss a pallet, or scan a pallet twice. RFID portals catch some of this but have their own ~4% read miss rate, especially with metal-content loads and high-humidity bays.
- **Cold-chain visibility gaps on pharma shipments are the single highest-severity issue.** Pharma customers (a growing book of business) require continuous 2–8°C custody records. Today the 3PL provides single-use temperature loggers per shipment, downloaded at destination. There is no real-time alerting, no in-transit excursion response, and roughly 0.6% of pharma shipments are rejected at delivery for cold-chain breach — often a breach that occurred hours earlier and could have been mitigated.
- **Impact and drop events are entirely invisible.** Damage claims are litigated after the fact with no telemetry. The customer pays out an estimated $400K/yr in disputed damage claims they suspect occurred at partner facilities.

The existing RFID + manual stack cannot be incrementally fixed. RFID portals don't move with the pallet. Handhelds depend on operator discipline. Single-use loggers don't stream. The customer is ready to fund a replacement telemetry layer purpose-built for pallet-grade economics.

## 2. Goals

1. Cut annual pallet loss rate from 6.8% to under 3.0% within 18 months of full rollout.
2. Eliminate manual scanning at dock-door inbound/outbound for tracked pallets (replaced by automatic device-reported location handoff).
3. Deliver real-time cold-chain visibility for 100% of pharma shipments, with excursion alerts inside the configured freshness budget.
4. Provide damage-event evidence (impact telemetry with timestamp + location) for disputed claims.
5. Hit a fully-loaded sensor unit economics target that allows full fleet deployment (50,000 units) inside the customer's approved capex envelope.
6. Deliver a hardware platform whose marginal cost of a sensor is low enough that loss is an opex line item, not a project-killing capex shock.
7. Establish an OTA-update discipline that lets us iterate firmware quarterly across a multi-region fleet without truck rolls.
8. Build a data foundation (pallet movement, dwell time, lane temperature profiles) that the 3PL can monetize as a value-add service to its own customers within 24 months.

## 3. Users and Personas

### Warehouse Manager (WM)
Runs a single DC. Cares about throughput, dock-door utilization, and whether her crew is scanning correctly. Wants a dashboard view per dock door and per yard zone. Will not tolerate workflow that slows down putaway. Power user of the per-site view; near-zero interest in cross-network views.

### Logistics Coordinator (LC)
Network-level role. Routes pallets between DCs and to/from customer sites. Cares about pallet pool balance ("do I have enough pallets at DC-07 for next week?"), in-transit ETAs, and lost-pallet recovery. Lives in the cross-network map view and the alert queue. Will export pallet movement data into the customer's ERP weekly.

### Cold-Chain Auditor (CCA)
Compliance role, often shared across pharma customer accounts. Owns the audit trail for temperature-controlled shipments. Needs immutable, exportable temperature histories with location stamps. Cares about data completeness and provenance more than UX polish. Lives in the historical replay view and the export tool.

### Sensor Fleet Operator (SFO)
Physical maintenance role. Owns the sensor population: commissioning new devices, decommissioning failed ones, swapping batteries (where applicable), tracking spares inventory. This is a hands-on operations persona, not a desk persona. Lives in the device lifecycle view, often via a ruggedized tablet on the warehouse floor. Cares deeply about the FRU swap procedure being fast and idiot-resistant.

### Stakeholders (non-user)
- **3PL VP of Operations.** Approves capex, owns the loss-rate KPI personally. Quarterly review cadence.
- **Pharma end-customer Quality Officer.** Receives the audit exports. Doesn't log into the dashboard but is the highest-stakes consumer of the data.
- **PalletPulse engineering on-call.** Pages on T2/T3 escalation; consumes the alerting telemetry on the platform itself.

## 4. Hardware Constraints

This section is load-bearing. Engineering and product agreements here drive everything downstream.

### 4.1 Battery
- **Target battery life: 5 years** of unattended operation at the nominal duty cycle defined in §4.4.
- **Cell: single 3V lithium primary coin cell (CR2477 form factor).** Coin cell, not AA, to keep the enclosure thin enough to fit between stringer boards without protruding. **No rechargeable chemistry in v1** — recharge logistics across 14 warehouses are infeasible and Li-ion ages poorly at the temperature extremes we ship into.
- **Battery is NOT field-replaceable in v1.** The enclosure is sonic-welded and potted for IP67. End-of-life means full unit replacement. This is a deliberate cost/reliability tradeoff and the SFO procedures must reflect it.
- Sleep current budget: <5 µA. Active TX current budget: <40 mA for <100 ms windows.

### 4.2 Radio
- **LoRaWAN Class A device.** Class A only — no Class B (no beacon-synchronized downlinks) and no Class C (no continuous RX). Class A is the only class consistent with the 5-year battery target.
- **Regional bands:** US915 for North American fleet, EU868 for European fleet. Devices are region-locked at provisioning; no roaming across bands.
- Backhaul via customer-owned gateways at each DC plus public LoRaWAN network coverage (Helium, Senet, etc.) in transit corridors.

### 4.3 Duty Cycle
- **EU868 enforces a 1% duty cycle per ETSI EN 300 220.** This caps an EU sensor at ~36 seconds of airtime per hour per sub-band. At SF10 / 125kHz, a typical uplink is ~370ms, giving a ceiling of roughly 90–100 uplinks/hour theoretical and meaningfully less in practice once retries and ADR are accounted for.
- **US915 has dwell-time limits (400 ms) but no duty-cycle cap.** This means the EU fleet is the binding constraint for any feature that needs frequent uplinks.
- All uplink-rate features (location ping interval, alert verbosity) must degrade gracefully under EU duty-cycle pressure. The product must never silently violate duty cycle.

### 4.4 Nominal Uplink Schedule
- Location ping: every 60 minutes when stationary (geofence-confirmed inside a DC).
- Location ping: every 15 minutes when in motion (accelerometer-triggered state change).
- Temperature ping: every 30 minutes for warm-chain pallets, every 10 minutes for cold-chain-flagged pallets.
- Impact event: immediate uplink on threshold trip, with 30-minute lockout to prevent storm.
- Heartbeat: every 24 hours minimum (presence confirmation).

These rates are tuned to land at the 5-year battery target while staying inside EU868 duty cycle with headroom for retransmits.

The cold-chain 10-minute interval is the binding case for EU868 sensors. Combined with location-in-motion at 15 minutes and the heartbeat, a cold-chain EU pallet in transit consumes roughly 18 uplinks/hour at worst case, ~6.7 seconds of airtime — comfortably under the 36-second/hour ceiling but with only ~5x headroom, which is the engineering margin we accept. Anything tighter (5-minute temperature, 5-minute location) blows the duty cycle and is rejected at the firmware layer before transmission.

### 4.5 Ruggedization
- **IP67** ingress protection. Survives temporary submersion (yard puddles, washdowns).
- **Operating temperature: -30°C to +70°C.** Storage: -40°C to +85°C. Pharma cold-chain often runs -25°C reefer; outdoor yards in Edmonton hit -35°C in winter. Spec must clear the worst case.
- Shock: survive 1.2m drop onto concrete (typical pallet handling), repeated. Sensor is mounted to the pallet stringer, not the deck boards, to reduce direct impact exposure.
- **UV-stable enclosure** (polycarbonate with UV inhibitor). Outdoor yard storage is the norm.

### 4.6 BOM and Unit Economics
- **Sensor BOM target: under $18** at 50K-unit volume. Current modeled BOM at preferred MOQ is $16.40 (LoRa SoC $4.20, accelerometer $0.85, temp sensor $0.40, GNSS module $5.10, antenna $0.60, PCB+passives $1.80, battery $0.95, enclosure $1.50, assembly+test $1.00).
- **GNSS is the single most expensive line item.** We are accepting that cost in v1 because gateway-based trilateration alone does not give us yard-level precision and is insufficient for in-transit visibility outside gateway coverage. v2 should revisit cell-aided GNSS or Wi-Fi positioning if a sub-$3 module becomes available.
- **Expected loss rate: 2% annually**, driven by pallet destruction, theft, and sensor-on-pallet end-of-life. At 50K units, that is **1,000 sensor replacements per year** baseline, ramping with fleet age. The replacement budget must be modeled into the customer's ongoing opex, not just initial deployment capex.

### 4.7 Mounting
- Sensor mounts to the pallet stringer via stainless steel banding + adhesive backing. **No screws into the wood** — screws back out over a pallet's working life. Mounting position is standardized (driver-side stringer, end-block-adjacent) so SFO replacement is muscle-memory.
- Banding is 304 stainless, 12.7mm width, sealless punch joint. Tools required: pneumatic tensioner or manual hand tool; both are stocked in the SFO kit.
- Adhesive backing is a secondary retention only — banding is primary. If banding fails, adhesive is expected to hold the sensor in place long enough for the next geofence event to flag the pallet for SFO inspection.

### 4.8 Regulatory and Certification
- **FCC Part 15 (US), ISED RSS-247 (CA), CE RED (EU)** at minimum. RoHS and REACH compliance mandatory for EU distribution.
- LoRaWAN Alliance certification on the radio stack — non-negotiable for roaming agreements with Helium / Senet / TTI.
- Battery shipping: UN3091 (lithium batteries packed with equipment). Manufacturing partner must hold IATA dangerous-goods shipping certification.
- Pharma end-customers will request a Quality Agreement covering the sensor's role in their cold-chain process. We are a data provider, not a GxP-validated system — clear scope demarcation required.

### 4.9 Failure Modes Anticipated in Field
Documenting these here because they shape downstream FRs and operational runbooks:
- **Battery EOL "cliff."** Lithium primary cells drop voltage abruptly near EOL. Sensors will not gracefully degrade — they will go from healthy uplinks to silent within days. The battery state model (FR-14) must catch this in advance; we cannot rely on a "last gasp" uplink.
- **Cold-soak induced silence.** Cells parked in a -25°C reefer for days may temporarily fail to source TX current. Sensor recovers when temperature rises. Backend must not auto-flag these as lost; the lost-pallet workflow (FR-15) needs a "cold soak" classification informed by last-reported temperature.
- **GNSS fix failure inside steel-roofed DCs.** Expected behavior; gateway-derived position takes over per FR-2. Display must make this clear so LC doesn't chase "drifting" positions that are just gateway estimates.
- **Banding fatigue.** Stainless steel banding can fatigue under repeated forklift handling. Expected to be a minority failure mode but must be classified at RMA so we can revisit banding gauge in v2 if it spikes.
- **Sensor co-location interference.** 50 sensors on a single trailer share the same RF environment. EU868 sub-band collisions are expected; LoRa's CSS modulation tolerates this well but uplink success rate will dip in high-density-pallet trailers. Modeled into NFR-1's 95% target.
- **Water ingress at IP67 seal.** Sonic-welded seal is rated IP67 but field abuse (impact at the seam, UV embrittlement over 5 years) will produce some ingress failures. Expected within the 2% loss budget.

### 4.10 Hardware Roadmap Beyond v1
Not commitments, but flagged so v1 architecture leaves doors open:
- v1.5: secondary cell chemistry option (LiSOCl2) for ultra-cold-chain SKUs.
- v2: cell-aided GNSS or Wi-Fi positioning to drop GNSS module cost.
- v2: optional NB-IoT cellular backhaul SKU for premium real-time pharma pallets.
- v2: humidity and ambient-light sensors for high-value cold-chain.

## 5. Functional Requirements

**FR-1 — Device Provisioning.** System shall support provisioning of new sensors via the SFO mobile app: QR-scan the device, scan the pallet ID, assign region (US915/EU868), assign customer/account. Provisioning completes within 60 seconds per device. Bulk provisioning mode for receiving new pallets from the manufacturer (batch of 500+).

**FR-2 — Location Reporting.** Each sensor reports GNSS-derived location per the §4.4 schedule. Indoor (no GNSS fix) falls back to gateway-derived position with explicit confidence flag. Stale-position display must visually distinguish fresh fix, stale fix, and gateway-estimated.

**FR-3 — Temperature Reporting.** Each sensor reports ambient temperature per the §4.4 schedule. Cold-chain-flagged pallets default to the 10-minute interval; the flag is set at the customer-shipment level via API or dashboard.

**FR-4 — Impact Event Detection.** Onboard accelerometer detects shock events exceeding a configurable g-threshold (default 8g over 50ms). On trip, sensor uplinks an event packet immediately with timestamp, last-known location, and peak-g reading. 30-minute lockout prevents alert storms during rough handling.

**FR-5 — Geofence Definitions.** LC can define geofences via map polygon: DCs, customer sites, partner yards, "off-network" exclusion zones. Each geofence has entry/exit alert rules. System supports a minimum of 500 active geofences per customer account.

**FR-6 — Alert Rules Engine.** LC and CCA can define alert rules combining: pallet attribute (cold-chain flag, customer, SKU group), geofence state, temperature threshold/duration, impact event, and battery state. Alerts route to email, SMS, webhook (for customer ERP integration), and in-app inbox.

**FR-7 — Cold-Chain Excursion Detection.** Specialized alert: any temperature reading outside the configured envelope (default 2–8°C for pharma) triggers an excursion alert. Excursion duration is calculated client-side from the temperature history. Confirmed excursions are flagged on the shipment record permanently.

**FR-8 — Dashboard: Site View.** WM-facing view: live map of pallets at their site, dock-door inbound/outbound activity, alerts for their site only. Auto-refresh, no manual reload required.

**FR-9 — Dashboard: Network View.** LC-facing view: live map across all 14 DCs and in-transit pallets, pool balance by site, alert queue, in-transit ETA estimates. Filters by customer, cold-chain flag, alert state.

**FR-10 — Historical Replay.** CCA-facing view: scrub the full telemetry history of a single pallet across any time range. Renders location track, temperature curve, and impact events on a unified timeline. Exportable as PDF (audit deliverable) and CSV (raw data).

**FR-11 — ERP Export.** Scheduled push of pallet movement events and dwell-time summaries to the customer ERP via SFTP CSV and via REST webhook. Configurable per-account. Includes idempotency keys for replay safety.

**FR-12 — Sensor Lifecycle Management.** SFO-facing view: device inventory (deployed, spare, retired, lost), per-device battery state estimate (modeled, not measured), per-device last-seen timestamp, replacement queue (devices flagged for swap). Bulk actions for end-of-quarter audits.

**FR-13 — Firmware OTA Updates.** System shall support over-the-air firmware updates to deployed sensors via LoRaWAN multicast (Class A fragmented data block transport, FUOTA). Updates are opt-in per device cohort, throttled to respect duty cycle, and verifiable via post-update version-report uplink. **Budget: full-fleet firmware update completes in under 14 days** under nominal gateway coverage.

**FR-14 — Battery State Modeling.** System computes a modeled battery state per sensor from cumulative uplink count, region, and operating temperature history. Surfaces "replace within 90 days" warnings to SFO. There is no direct battery voltage telemetry in v1 (every uplink to report voltage burns budget); modeling is good enough.

**FR-15 — Lost Pallet Workflow.** Pallets that miss N consecutive heartbeats (default N=3, so 72 hours silent) auto-flag as "presumed lost." LC sees them in a dedicated queue with last-known location and last-known geofence. Recovery action can be marked, escalated, or written off.

**FR-16 — Multi-Tenancy.** System supports multiple end-customers of the 3PL with strict data isolation. A pharma customer cannot see another pharma customer's shipments. The 3PL itself sees everything.

**FR-17 — Audit Log.** All user actions (geofence edits, alert rule changes, device assignments, manual overrides) are logged immutably for 7 years. Required for pharma compliance audits.

**FR-18 — API.** RESTful API exposing read-access to pallet state, telemetry history, and alerts. Used by customer ERP integrations and by future internal tooling. OpenAPI spec maintained as source of truth.

### 5.1 Cross-Cutting Behavior Requirements
- **Idempotency.** All uplinks carry a monotonic device-side sequence number. Backend deduplicates by (device-id, seq) so gateway-level duplicates do not double-count.
- **Time sync.** Sensors do not carry a real-time clock; timestamps are gateway-applied at ingest. UI must always show "received at" alongside any synthesized "occurred at" estimates.
- **Backfill on reconnect.** When a sensor exits a coverage gap, queued events (impact, geofence transitions) uplink in priority order before resuming normal cadence.
- **Privacy.** Location data of pallets parked at end-customer sites is end-customer-confidential. The 3PL has access; other tenants do not. Enforced at API layer.

### 5.2 Notes on Requirements Not Yet Numbered (v1.1 candidates)
The following are explicitly deferred but tracked here so the architecture doesn't paint itself into a corner:
- Predictive battery-EOL clustering (group sensors for batch replacement campaigns).
- Lane-level temperature profiles (aggregate across pallets traversing the same route).
- Customer-facing read-only portal for the 3PL's end customers.
- Mobile push notifications for the WM persona (currently SMS/email only).

## 6. Backend Architecture Notes

Brief, because the hardware is the hard part:

- **Gateway layer:** Customer-owned LoRaWAN gateways (Multitech / RAK 8-channel class) at each DC backhaul over wired internet. Roaming agreement with Helium / Senet covers transit corridors in NA; covered separately by The Things Industries roaming in EU.
- **Network server:** Managed LNS (ChirpStack or TTI). We do not run the LNS ourselves in v1.
- **Ingress:** LNS publishes decoded uplinks to MQTT broker (managed, multi-AZ).
- **Stream processor:** Consumes MQTT, normalizes, applies alert-rule evaluation, writes to time-series DB and to the alert queue.
- **Time-series DB:** Telemetry retention. Hot tier 90 days, warm tier 7 years (pharma audit retention).
- **Operational DB:** Postgres for devices, geofences, accounts, alert rules, audit log.
- **API:** Stateless service, behind regional load balancers.
- **Multi-region:** US-East and EU-Central deployments, with data residency enforced — EU customer data does not leave EU.
- **Identity:** SSO via SAML / OIDC for 3PL employees; per-account local users for end-customer logins. MFA required for any role that can edit alert rules or trigger OTA.
- **Observability:** Backend metrics, structured logs, and traces shipped to the platform's own monitoring stack. Sensor-fleet health is treated as a first-class observability surface, not an afterthought (uplink success rate per gateway, per region, per firmware version).

## 7. Non-Functional Requirements

**NFR-1 — Uplink Reliability.** 95% of scheduled uplinks shall successfully reach the backend, measured on a 7-day rolling window per region. Below 95% in any DC triggers gateway-coverage review.

**NFR-2 — Data Freshness (warm-chain).** Median time from sensor uplink to dashboard visibility shall be under 15 minutes. p95 under 30 minutes.

**NFR-3 — Data Freshness (cold-chain).** Median time from sensor uplink to dashboard visibility for cold-chain-flagged pallets shall be under 5 minutes. p95 under 10 minutes. Alert evaluation latency inside this budget.

**NFR-4 — Backend Availability.** 99.5% monthly availability for the API and dashboard. Scheduled maintenance windows excluded with 7-day notice.

**NFR-5 — Multi-Region Data Residency.** EU customer data shall be stored and processed in EU region only. No cross-region replication of customer telemetry.

**NFR-6 — Audit Trail Retention.** Telemetry and user-action logs retained for 7 years to satisfy pharma audit requirements.

**NFR-7 — Localization.** Dashboard supports English, French, German, Spanish at GA. Date/time, units (Celsius mandatory for cold-chain — no toggle), and currency localized.

**NFR-8 — Security.** All data in transit is encrypted (TLS 1.3 for backend, AES-128 LoRaWAN payload encryption for over-the-air). At-rest encryption on all telemetry stores. Annual penetration test; quarterly dependency-vulnerability sweep.

**NFR-9 — Scale Headroom.** Architecture sized for 250,000 sensors at peak uplink rate without re-platforming. The current 50,000-pallet target is the anchor customer, not the cap.

**NFR-10 — Cost-per-Uplink Ceiling.** Backend cost-to-serve per uplink (compute + storage + network operator fee) must stay under $0.0008 at GA scale. This is a hard product economics constraint; instrument it from day one.

## 8. Operational Requirements

**OR-1 — Sensor FRU Procedure.** Sensor is a field-replaceable unit. SFO workflow: locate flagged pallet via mobile app, cut existing steel banding, remove old sensor (deposit in returns bin for tear-down analysis), apply new sensor with new banding, scan-pair to pallet ID, confirm. Target swap time: under 3 minutes per pallet. **No tools required beyond banding cutter and tensioner.** Mobile app guides the SFO step-by-step and blocks confirmation until both the old sensor's decommission packet and the new sensor's first uplink are received. This is the single most-executed operational procedure in the system; UX investment here pays back fastest.

**OR-2 — Gateway Commissioning.** Each new DC requires a gateway commissioning runbook: site survey for RF (account for racking, metal roof), gateway placement and antenna, backhaul provisioning, LNS registration, coverage test with calibration sensor walked through the facility. Target: gateway live within 1 business day of equipment on site.

**OR-3 — Customer Onboarding.** New end-customer (pharma client, etc.) onboarding flow: account provisioning, user invites, geofence definition for their sites, alert rule templates, ERP integration setup, training session. Target: onboarded within 5 business days.

**OR-4 — Support Escalation.** Three-tier escalation: T1 (3PL ops center, dashboard/alert questions), T2 (PalletPulse support, device-level issues), T3 (engineering, suspected firmware/backend bugs). Runbook references maintained per known failure mode: lost-gateway, mass-uplink-silence, OTA-rollback, false-positive-impact-storm.

**OR-5 — Sensor RMA and Tear-Down.** Returned sensors are inspected to classify failure: battery EOL, water ingress, physical damage, electronics failure. Feeds into the BOM and reliability model. Minimum 1% of returns get full tear-down analysis.

**OR-6 — Spares Inventory Policy.** Each DC holds local spares equal to 4% of pallet-on-site count (2x the modeled annual loss rate, to absorb spikes). Central spares pool of 1,000 units in NA and 500 in EU. Reorder triggers fire automatically from the device lifecycle view.

**OR-7 — Manufacturing Yield and QA.** Manufacturing partner must hold 98% first-pass yield at EOL test. Each batch ships with a yield report; batches under 95% are quarantined for engineering review. Sample sensors from every batch get 30-day burn-in before bulk deployment.

**OR-8 — Firmware Release Discipline.** Firmware releases are signed, version-pinned, and rolled out in cohorts: 1% canary → 10% wave → 50% wave → 100%. Automatic rollback on uplink-failure-rate regression of >5 percentage points in any cohort. Firmware release notes are customer-visible.

## 9. Risk Register

**R-1 — LoRaWAN gateway coverage gaps in transit corridors.** Public LoRaWAN coverage in long-haul corridors (especially rural US and parts of Eastern EU) is patchy. Pallets may go silent for hours mid-transit. *Mitigation:* set heartbeat expectations correctly in the UX, surface "expected silent zone" coloring, prioritize Class A store-and-forward of buffered uplinks. *Owner:* Networks lead.

**R-2 — Battery degradation in extreme cold.** Lithium primary cells lose capacity below -20°C. Pharma reefer at -25°C, Canadian outdoor yards in winter, deep-freeze cold-chain (rare but possible) all stress the 5-year target. *Mitigation:* battery state model is temperature-aware; cold-shipment-heavy pallets get accelerated replacement cadence; v2 evaluates LiSOCl2 alternative chemistry. *Owner:* Hardware lead.

**R-3 — Sensor theft and tampering.** $18 BOM is not high-value but the sensor advertises that a pallet is tracked, which is a theft deterrent for the pallet — and possibly an attraction for sensor theft. *Mitigation:* tamper-evident banding, sensor reports last-seen-on-pallet status, geofence-exit-while-stationary detects sensor removal. Accept that some theft will occur within the 2% loss budget. *Owner:* Product.

**R-4 — False-positive impact alerts.** Forklift handling routinely creates 5–10g events. An 8g threshold is calibrated against bench data, not field data. We expect tuning in pilot. *Mitigation:* impact events ship with peak-g and duration; backend filters by combined signature; threshold is per-account configurable. Alert fatigue is the real risk here, not the alert itself. *Owner:* Firmware + Product.

**R-5 — Public LoRaWAN network operator pricing changes.** Helium and Senet pricing per-uplink is the largest opex line item after sensor replacement. Operator margin compression or consolidation could spike per-uplink cost 2–3x. *Mitigation:* contract minimum 24-month rate locks with primary operator, model alternate-operator switching cost, design uplink schedule to allow dialing back rate if cost forces it. *Owner:* Finance + Networks.

**R-6 — Regional regulatory change (duty cycle, spectrum allocation).** EU868 duty cycle could tighten; US915 allocation could shift. Either invalidates the deployed firmware's transmission scheduler. *Mitigation:* duty-cycle parameters are configurable via OTA, not hardcoded; firmware is structured for regional parameter updates without full reflash. *Owner:* Firmware lead.

**R-7 — Manufacturing partner single-source risk.** v1 BOM assumes a single contract manufacturer. Disruption (geopolitical, fab capacity, fire) stalls the replacement pipeline. *Mitigation:* qualify a second-source CM by GA + 6 months; design files held in escrow; critical components have approved alternates documented in the BOM. *Owner:* Hardware lead + Supply Chain.

## 10. Milestones

**M1 — Pilot Hardware Build (Month 0–2).** 500 pilot units manufactured, EVT/DVT cleared, regulatory cert (FCC, CE, IC) in hand or in-flight. Backend MVP deployed to staging.

**M2 — Pilot Deployment, 2 Warehouses (Month 2–4).** Deploy 500 sensors across DC-01 (Chicago) and DC-08 (Rotterdam). Validate gateway coverage, uplink reliability, FRU procedure, dashboard UX with real WM and LC users. **Pilot exit gate:** NFR-1 met, FRU under 3min average, zero P0 firmware regressions in final 4 weeks.

**M3 — Pilot Iteration (Month 4–5).** Address pilot findings: impact threshold tuning, dashboard refinements, OTA pipeline hardening. Manufacturing partner ramped for full-fleet build.

**M4 — Wave 1 Expansion (Month 5–6).** 6 additional DCs (3 NA, 3 EU). ~15,000 sensors deployed. First pharma customer onboarded for live cold-chain.

**M5 — Wave 2 Expansion (Month 6–7).** Remaining 6 DCs. ~20,000 additional sensors.

**M6 — Full Fleet GA (Month 7–8).** All 14 DCs, full 50,000-pallet population sensorized. Customer ERP integration live. Audit-grade historical replay live for pharma accounts. M6 + 30 days: 18-month KPI baseline measurement begins.

### 10.1 Milestone Gates and Exit Criteria
Each milestone has an explicit go/no-go gate:
- **M1 gate:** EVT cleared with 0 P0 hardware defects; 95% uplink reliability on 50-unit bench-and-yard test over 14 days.
- **M2 gate:** Pilot meets NFR-1, NFR-2, NFR-3 at the 2-DC scale; SFO FRU swap time under 3 minutes averaged across 50 swaps; WM and LC NPS at the site level non-negative.
- **M3 gate:** Impact threshold false-positive rate under 5% on a 30-day window; OTA rollout completes a full pilot-fleet update inside 7 days without rollback.
- **M4 gate:** First pharma cold-chain shipment completes with full telemetry and zero data gaps; auditor sign-off on the historical replay export format.
- **M5 gate:** Network-view performance under load at 35,000 active sensors; alert-rule engine evaluating under the freshness budgets.
- **M6 gate:** Full-fleet uplink reliability holding at 95% across 30 days; cost-per-uplink under the NFR-10 ceiling; customer-facing SLA in effect.

## 11. Non-Goals (v1)

- **NOT an RFID replacement for inventory at SKU/case level.** Pallet-level only. SKU-level tracking is out of scope and out of the BOM budget.
- **NOT a Warehouse Management System.** No putaway optimization, no slotting, no pick paths. We feed the customer's existing WMS via ERP export.
- **NOT cellular fallback in v1.** LoRaWAN only. Cellular backhaul on the sensor would blow the battery budget and the BOM. v2 will reconsider for a premium cold-chain SKU.
- **NOT consumer-package tracking.** Outside customer use case, outside form factor, outside regulatory model.
- **NOT a return-loop pallet pool service.** We track the customer's pool. We don't operate a CHEP-style shared pool.
- **NOT humidity, light, or tilt sensing in v1.** Location, temperature, impact only. Additional modalities are v2 candidates if pharma demand justifies them.

## 12. Success Metrics

Measured at GA + 12 months unless otherwise noted.

- **Pallet loss rate:** baseline 6.8% → target under 3.0%.
- **Manual scan volume at dock doors:** baseline 100% of pallets scanned → target under 10% (exception scans only).
- **Cold-chain excursion detection rate:** target 99% of excursions detected and alerted inside the 5-minute freshness budget (NFR-3).
- **Sensor uptime / uplink reliability:** target 95% of scheduled uplinks delivered, sustained (NFR-1).
- **Mean time to recover a lost pallet:** baseline 11 days → target under 48 hours for pallets last seen on-network.
- **Disputed damage claims with telemetry evidence:** target 90% of disputed claims have impact-event telemetry to support or refute.
- **Sensor field reliability:** target under 2% annual replacement rate matches §4.6 modeled loss rate.
- **Customer NPS:** measured at the 3PL operations leadership level, target +40 at GA + 12 months.
- **Battery-life realization:** target 80% of deployed sensors still operating at year-5 against the modeled state (lagging indicator, measured at the year-5 cohort, not at GA+12).
- **Cost-per-uplink at GA scale:** target under $0.0008 (NFR-10), tracked monthly.
- **OTA campaign completion time:** target full-fleet firmware update inside 14 days (FR-13), measured per campaign.
- **First-pass manufacturing yield:** target 98%, tracked per batch.

## 13. Open Questions

- Confirm pharma-customer Quality Agreement template with first pharma account before M4. Their counsel will drive scope.
- Decide v1 stance on customer-owned vs. PalletPulse-owned gateways at the 14 DCs. Leaning customer-owned (capex shifts to them, support shifts to them), but it affects R-1 mitigation.
- Validate Helium roaming SLA in the Midwest US corridor; pilot data will tell us if it's investment-grade or a placeholder.
- Decide retention policy for the warm tier in non-pharma accounts. 7 years is pharma-driven; non-pharma may not justify the storage cost.

## 14. Dependencies and Assumptions

**Dependencies:**
- Customer commits to gateway installation runway aligned with M2–M5 (any DC behind schedule pushes its sensors back).
- Manufacturing partner holds capacity for 50,000-unit ramp inside the M3–M5 window.
- Helium and Senet roaming agreements signed before M4 (in-transit coverage gate).
- Pharma end-customer signs Quality Agreement before M4 cold-chain go-live.

**Assumptions:**
- 3PL retains its current pallet pool size (~50,000) within +/- 10% over the rollout period.
- LoRaWAN regulatory regime in EU868 and US915 is stable through GA + 24 months.
- GNSS module supply (the long-lead BOM line) holds at current pricing.
- Customer's existing WMS exposes a stable integration surface for ERP export (FR-11).
- Pallet handling practices at the 14 DCs are consistent enough that a single impact-threshold default (8g/50ms) is a reasonable starting point.

## 15. Appendix: Glossary

- **BOM:** Bill of Materials. The component cost roll-up for a single sensor unit.
- **Class A device:** LoRaWAN device class with two short receive windows after each uplink. Lowest power consumption class; mandatory for battery-driven sensors.
- **Duty cycle:** Regulatory limit on the fraction of time a transmitter may occupy a frequency band. EU868 is 1% per sub-band; US915 uses dwell-time limits instead.
- **FRU:** Field-Replaceable Unit. A unit designed to be swapped in the field without depot return.
- **FUOTA:** Firmware Update Over-The-Air. LoRaWAN-specific multicast firmware delivery protocol.
- **Geofence:** A polygon on a map defining a logical zone (site, yard, exclusion area).
- **LNS:** LoRaWAN Network Server. Manages the radio network and decoded payload routing.
- **MOQ:** Minimum Order Quantity. The smallest batch a manufacturer will produce at a quoted unit price.
- **OTA:** Over-The-Air (update). General term covering firmware and configuration updates delivered wirelessly.
- **SF (spreading factor):** LoRa modulation parameter trading airtime for range/sensitivity. Higher SF = longer range, longer airtime, more duty cycle consumed.
