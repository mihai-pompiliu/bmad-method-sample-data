# Decision Log — Plantsona PRD

Canonical memory and audit trail for this PRD session. Every meaningful decision, change, override, and deferral lands here as it happens.

---

## 2026-05-11 — Session opened

- **Intent:** Create new PRD.
- **Source input:** `_bmad-output/planning-artifacts/briefs/plantsona-2026-05-01/distillate.md` — brief distillate produced in headless mode from one-paragraph concept; many specifics inferred and flagged for user confirmation.
- **Workspace:** `_bmad-output/planning-artifacts/prds/prd-plantsona-2026-05-11/`
- **Working title:** Plantsona (alternatives surfaced in distillate Open Questions: Sprout, Leafling, Plantonia, Bloomr, Verdant, Florafriends, Petal, Rooted, Botany — pending user decision).

## 2026-05-11 — Discovery: shape confirmed

- **Distillate fidelity:** "Spot on — use as-is." Treat distillate content as source of truth; its Open Questions are the only real unknowns.
- **PRD purpose:** Public BMad showcase. Every section earns its place. Visible discipline (Glossary, Assumptions Index, Non-Goals, counter-metrics, traceability) is intentional pedagogy.
- **Working title decision:** Keep "Plantsona" as working title in PRD; brand finalization deferred. Logged in §8 Open Questions.
- **Adapt-in clusters selected:** Why Now, Aesthetic & Tone, Information Architecture, Platform, Monetization, Cross-Cutting NFRs, Constraints & Guardrails (Safety/Privacy/Cost). Skipping Enterprise clusters, heavy Compliance, API Contracts, Embedded, Small-scope all-inclusive.
- **PRD purpose framing:** Consumer venture PRD heading into full chain (UX → architecture → stories).

## 2026-05-11 — Decision: v1 narrative spine = milestone-driven feed

- **Wedge chosen:** Milestone-driven feed (Strava-for-plants). Care events → in-character milestone posts → social loop.
- **Why:** Most familiar mechanic, easiest to explain, fastest to populate feed organically — answers "what is this when I open it?" in one sentence.
- **Acknowledged competitive risk:** Closest to what Greg could ship within 90 days. User accepted this tradeoff; mitigation = voice tooling + accumulated longitudinal identity moat (per distillate). Will surface explicitly in §Risks or Open Questions.
- **Propagation lineage and seasonal cohorts remain v1 features** but secondary to milestone narrative. They are differentiation depth, not the opening hook.

## 2026-05-11 — Decision: launch surface = web + PWA primary, iOS in seed metros

- **Decision:** Web responsive + PWA at launch (everywhere), native iOS shipped concurrently in seed metros (Brooklyn, Austin, LA). Android deferred to v2.
- **Why:** Lowest distribution friction for QR-claim viral loop (web = instant claim, no app store); best fit for nursery partnership rollout; iOS in seed metros covers collector demographic where care-reminder push fidelity matters most.
- **Acknowledged trade:** iOS PWA push notifications are weak/throttled — care-reminder parity with Planta/Greg degrades for users on web/PWA only. Mitigation: seed-metro iOS native covers the highest-engagement collectors; web/PWA users get email + in-app reminders. To surface in §Platform and Cross-Cutting NFRs.

## 2026-05-11 — Finalize: input reconciliation against distillate

- **Reconciliation report:** Source-extractor subagent surfaced Bucket A (concrete adds compressed out: stats, Reddit/Discord, scenarios, Adjacent Value, Source Provenance), Bucket B (soft ideas dropped: three-layer thesis, moat thesis as data+execution, 3-year vision, "joyous" tone, Greg-vacuum framing, care-parity urgency), Bucket C (override acknowledgments for OQ-8 wedge and §Platform).
- **Decision:** Incorporate everything (Buckets A + B + C). Materially upgrades showcase quality.
- **Care Hub IA decision:** Keep Care Hub as separate top-level surface. Composer remains dominant care-logging path; Care Hub is the utility-parity safety net for dormant/winter periods. The "social loop primary" thesis is preserved by Composer's role; Care Hub is the parity floor.
- **Brand Profile premium ineligibility:** Defer to v1.5 spec. Strike the "not eligible for premium-tier features" line from §Glossary. The Channel Partner monetization model belongs in v1.5 spec when nursery tooling actually ships.
- **PRD restructured:** new top-level sections added — §3 Three-Year Vision, §4 Competitive Landscape, §18 Post-v1 Adjacent Value. §1 Vision expanded with §1.2 thesis and §1.3 moat. Total numbered sections: 0-20 (was 0-17). Cross-references in §0 corrected.

## 2026-05-17 — Spec distilled from PRD

- **Operation:** `bmad-spec` distillation, create (no prior spec.md at target).
- **Input:** sibling `prd.md` — rich ceremony artifact; extracted directly without elicitation.
- **Output:** sibling `spec.md` (co-located in workspace).
- **Capability shape:** 11 capabilities (CAP-1..CAP-11) consolidating 44 PRD Functional Requirements. FR-level detail folded into each capability's `success`, not enumerated 1:1. IDs stable from this distillation forward.
- **Constraint set:** 12 constraints — each rules a real design path out (no-generic-AI, archive-not-delete, zip-cluster geo, no-Android, single-tap latency, etc.).
- **Non-goals:** 11, lifted directly from PRD §14 with platform/v1 framing.
- **Success signal:** Liquidity Unit threshold (≥60% seed-metro users with 5+ followers AND 3+ in-character interactions within 14 days) paired with ≥60% in-character interaction rate on Milestone Posts. Demonstrable on cohort.
- **Assumptions carried:** All 11 PRD inline assumptions (A1–A11) plus A12 — the distillation choice to consolidate FRs into capability-scope `success` statements.
- **Open questions carried:** PRD OQ-1 through OQ-14 verbatim plus new OQ-15 — CAP-11 concierge cohort active-posting threshold needs a numeric floor to make the success criterion testable.
- **Self-validate verdict:** Ready for downstream. All 11 capabilities have both intent and success; intents are WHAT not HOW; constraints all rule something out; non-goals explicit; success signal cohort-testable; capability IDs unique and stable. One concrete gap (OQ-15) was raised by the validation pass itself — the input did not supply a numeric posting floor for the concierge cohort.
