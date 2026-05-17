# Decision Log — Heart Rate Monitor Portal PRD

## 2026-05-15 — Session 1 (Discovery + Framing)

- **Intent:** Create.
- **Scope:** Portfolio project; full BMad chain downstream (PRD → UX → architecture → epics → stories).
- **Working mode:** Facilitative.
- **MVP shape:** Experience MVP — proving the interaction model of a clinical monitoring dashboard.
- **Persona depth:** Single primary persona (doctor); no persona theater given portfolio scope.
- **Data source:** Fake API (no real device integration, no manufacturer cloud).
- **"Remote enable detected" interpretation (confirmed):** Each patient's monitor has a status flag indicating it is actively transmitting; the doctor's view scopes to assigned patients whose monitors are currently active.
- **Doctor capability scope (confirmed):** Live readings, historical trends, alerts on thresholds, patient list with status, drill-down per patient — all of it.

## Open framing items (to resolve during walk)

- Title: "Heart Rate Monitor Portal" — kept (user confirmed).
- Compliance posture: portfolio + fake data → skip regulated-domain cluster, but flag in Non-Goals.

## §1 Vision decisions

- **Problem framing confirmed:** scattered tools today — device portals, charts, alert systems — the portal collapses these.
- **Usage mode confirmed:** periodic review is primary; active monitoring is secondary. Downstream implication: home screen optimizes for "who needs attention" scanning, not live waveform displays. Patient detail view supports live updates as the secondary mode.
- **Positioning:** triage and review surface, not a monitoring station.

## §2 Target User decisions

- **Persona:** outpatient cardiologist (Dr. Lena Park). Single persona, no theater.
- **Two scopes per Doctor:** Active Panel (10–30 patients, default view) + Supervisory Panel (up to several hundred, search-only). Drives dashboard design as two-tier, not single-list.
- **Non-Users in v1:** patients, nurses/MAs/care coordinators, admins. Single-role product.
- **Three confirmed UJs:** between-patients triage (UJ-1), live watch (UJ-2), supervisory pull (UJ-3). User indicated these cover the product shape; no fourth UJ added.

## §3 Glossary decisions

- **Freshness window for Remote-Enabled = 10 minutes.** Confirmed.
- **Thresholds in v1: system-wide defaults only.** Per-Patient threshold tuning deferred to a later version. Confirmed.
- **Acknowledgment carries an optional Note** — kept as part of acknowledgment rather than a separate notes feature. Confirmed.

## §4 Features decisions

- **Feature slice (confirmed):** Authentication & Session, Active Panel, Patient Search & Supervisory Access, Patient Detail View (with Live Mode as sub-capability), Alerts & Acknowledgment.
- **Live Mode kept inside Detail View**, not split out as its own feature.
- **Monitor-quiet detection kept inside Alerts** as Quiet Monitor Alert type, not a separate feature.
- **Numeric/policy decisions confirmed:**
  - Session inactivity timeout: 8 hours.
  - Trend windows: 24-hour, 7-day, 30-day.
  - Live Mode update cadence: at most every 5 seconds.
  - Multi-tab Live Mode: undefined in v1, must not corrupt server state.
  - Threshold excursion semantics: one Alert per excursion; new Alert eligible only after Readings return inside band.
  - Note length: 500 characters.
  - Alert history: most recent 100 per Patient, no pagination.
- **`[NOTE FOR PM]` standing in §4.5:** per-Patient Thresholds — first capability extension after MVP.

## Finalize — Reviewer Triage Decisions

Three reviewers ran in parallel (structural checklist, adversarial general, edge-case hunter). Verdicts:

- **Structural checklist:** PASS; two low-severity cosmetic notes only.
- **Adversarial:** 27 findings (7 flagged pre-architecture blockers, rest tightening).
- **Edge-case hunt:** 31 unhandled edge cases across §4.1–§4.5 and cross-cutting.

Reports remain in workspace as `review-structural-checklist.md`, `review-adversarial.md`, `review-edge-cases.md`.

### Autofix pass (applied to prd.md)

- Added §3.1 System-wide Defaults table as single source of truth for: Freshness Window (10m), Thresholds (40/120 bpm), Quiet Alert Dedup (15m), Active Panel Soft Cap (60), Refresh Cadence (30s), Alert History Cap (100), Note Length Cap (500), Session Inactivity (8h), Live Mode Update Bound (5s).
- Glossary: added Never-Transmitted Monitor, Excursion, Freshness Window, Doctor Session. Tightened Remote-Enabled / Quiet / Threshold / Alert / Acknowledgment / Active Panel definitions. Confirmed Active and Supervisory Panels are mutually exclusive per Doctor.
- FR-2: singleton sessions enforced; new sign-in invalidates prior.
- FR-3: Last-Check Marker pinned to one definition (server-recorded end of prior session); first-ever sign-in sets to `now()` to avoid wall-of-new; Live Mode does not advance the marker; server-authoritative comparison.
- FR-4: Active Panel auto-refresh policy (30s + on mount + manual); soft cap at 60.
- FR-5: status encoded by color AND non-color carrier; *flagged* uses ALL unacknowledged Alerts, not only the visible 100; row identity = Patient ID (survives rename).
- FR-6: deterministic tiebreaker (Patient ID); cross-group transition uses transition timestamp.
- FR-7: clarified *normal* row may carry "new" indicator; per-Alert created-time check independent of prior panel membership.
- FR-7b (NEW): Active Panel summary counts for UJ-1 climax.
- FR-9: case-insensitive diacritic-insensitive substring match; 2+ grapheme clusters.
- FR-10: results unambiguous because Active/Supervisory mutually exclusive (FR-10 fix).
- FR-11: persistent banner phrasing chosen (above header, sticky during scroll + Live Mode).
- FR-13: Detail View shows Never-Transmitted state distinctly; stale-Reading visual when older than Freshness Window.
- FR-16: navigation-away terminates Live Mode; reload reopens in static; sticky acknowledgment dialog across re-renders; failure backoff affordance; works on Never-Transmitted Monitors.
- FR-17: precise Excursion boundary semantics (`>= upper` opens, `< upper` closes); first-Reading-out-of-band generates Alert; Quiet period closes any open Excursion.
- FR-18: 15-min dedup between consecutive Quiet Alerts; Monitor recovery leaves open Quiet Alert in *new* state until acked.
- FR-19: trim whitespace, grapheme-cluster count, server idempotency, server-side ack re-check on submission.
- FR-20: older-unacknowledged-Alerts (N) affordance; cap is display only, not authority on *flagged* status.
- §4.5 description: ack-of-Supervisory promoted with attribution.
- §6.1: rewording to match new FRs; new bullets for soft-cap, refresh cadence, dedup window, banner, attribution.
- §6.2: added Alert history pagination, multi-device concurrent sessions as out-of-scope explicitly.
- §7.1 SM-P2: drop FR-7 from validation list; pre-staged Session precondition; sharpened viewer criterion ("identify which Patient, why, and what action").
- §7.2 SM-A2: bound to inactivity-defined "between sessions" window.
- §8 Performance — Live Mode: softened from 1.5s P95 to "demonstrable on fake API"; deleted "2 in-flight requests/sec/Patient" implementation leak.
- §8 Security: fake-API contract caveat added; render-time and submission-time authorization re-checks called out.
- §9.3: timezone source defined (Doctor's browser TZ with explicit label; UTC stored).
- §11: rewrote to 8 phase-relevant questions covering items deferred from edge-case review.
- §12: rewritten honestly ("no [ASSUMPTION] tags remain; inferences tracked in §11").

### Discuss-cluster decisions (PM made the call)

- **Multi-device sessions:** singleton.
- **Active Panel size:** soft cap at 60.
- **Quiet Alert dedup:** 15-min window.
- **Threshold defaults:** 40 / 120 bpm with §11 cardiology sanity-check.
- **Live Mode NFR:** softened to fake-API stopwatch demonstration; no P95 target encoded.
- **"2 in-flight requests/sec/Patient" NFR:** deleted (implementation leak).
- **Patient on both panels:** mutually exclusive at data layer.
- **Search match semantics:** case-insensitive, diacritic-insensitive substring on full name.
- **Ack of Supervisory Alerts:** allowed with attribution; promoted to §4.5 explicitly.

### Deferred to §11 Open Questions

Trend chart partial-data rendering, "fresh open" definition, mid-session membership change UX, empty-account state, concurrent-ack UX surfacing, Threshold change during open Excursion, default-Threshold cardiology sanity-check, fake-API contract design.

### Ignored

- Persona `[ILLUSTRATIVE]` tag (D-3): portfolio scope; accepted.
- "Freshness window" as standalone Glossary entry (S-1): superseded by §3.1 System-wide Defaults.

## Finalize — Editorial Polish

Two reviewers ran in parallel (bmad-editorial-review-structure, bmad-editorial-review-prose). Verdicts: both strong, no rewrites; targeted local polish only.

### Structural polish applied

- **C1 / CO1 / CO4 / CO5:** §4.1 Authentication Description trimmed from 5 sentences to 2; singleton-session restatement reduced to single Glossary cross-reference; server-authoritative authorization centralized in §8 Security with cross-references from FR-3 and FR-19.
- **CO2:** Supervisory banner spec consolidated to FR-11; §4.3 Description shortened.
- **R2:** §4.4 FR-16 Live Mode consequences re-grouped under Happy path / Lifecycle / Concurrency invariant / Edge handling subheadings; sticky-Acknowledgment-dialog invariant promoted to a labeled bullet ("load-bearing for UJ-2").
- **R3:** §4.5 FR-19 consequences reordered (Quiet ack semantics moved ahead of Note constraints).
- **H2:** §3.1 heading renamed to "System-wide Defaults (single source of truth)"; lead-in revised to declare normative status.
- **H5:** §4.5 redundant `[NOTE FOR PM]` callout replaced with one-line pointer to §6.2.

### Prose polish applied

- **High:** §4.3 "for one of their primary panel" → "for one on their primary panel"; §4.5 "surfaced two places" → "surfaced in two places"; §8 Security subject-object inversion ("checks that any Patient a Doctor accesses is on...").
- **Medium:** §4.2 FR-5 "flips" → "transitions"; FR-6 run-on split; FR-16 sticky-dialog wording sharpened; FR-18 Monitor-recovery run-on split; FR-19 "without overwriting" → "without overwriting the original"; §3 "Active Panel inclusion is provided by..." → "The back end determines..."; §3 "is currently transmitting" → "is transmitting".
- **Low:** §0 antecedent chain rewrite; §1 "the doctor"/"clinician" drift resolved; §2 triple-"patients" repetition reduced; §3.1 colon-fragment cleanup; §7 "two layers honestly" → "two layers"; SM-A2 trailing-clause rewrite; §8 Accessibility "not just hue" redundancy removed; §11 Q5 tightened.

### Polish items deferred (low value, accepted as-is)

- H1 (§0 heading rename) — minor.
- H3 (§7.1 / §7.2 label trim) — style choice.
- H4 (FR-7b suffix numbering) — accepted; downstream artifacts handle it.
- R1 (§3.1 "Used in" column expansion) — net +5 lines; low payback.
- C2 (§0 paragraph 2 first sentence) — kept; provides shape preview for readers entering cold.
- "Color-vomit" (§9.2) — kept; anti-reference heat is intentional.
- "back end" / "back-end" — adjectival uses already hyphenated post-edits; nominal uses unhyphenated; no full sweep needed.

## Finalization

- PRD final state: ~21 FRs (FR-1 through FR-20 plus FR-7b), 3 UJs, ~12 sections, 9 deferred Open Questions, 0 unresolved assumptions.
- Artifacts in workspace `/Users/brianmadison/bmad-code/_bmad-output/planning-artifacts/prds/prd-heart-rate-portal-2026-05-15/`:
  - `prd.md` — finalized PRD
  - `decision-log.md` — this file
  - `review-structural-checklist.md` — PASS
  - `review-adversarial.md` — 27 findings, all dispositioned
  - `review-edge-cases.md` — 31 unhandled edge cases, all dispositioned
  - `review-editorial-structure.md` — 16 structural polish items
  - `review-editorial-prose.md` — prose polish items
- No addendum was created.
- External handoffs: none configured.
- Ready for next phase: UX design (skill: bmad-create-ux-design).
