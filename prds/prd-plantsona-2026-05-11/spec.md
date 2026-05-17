---
id: SPEC-plantsona
created: 2026-05-17
updated: 2026-05-17
status: draft
source_artifact: _bmad-output/planning-artifacts/prds/prd-plantsona-2026-05-11/prd.md
---

# Problem

Plant owners — the Collector (25–40, 5–50+ plants, $20–$80/mo, posting plant content on Instagram today) and the New Plant Parent (1–2 plants, intimidated, googling care) — have no place online where their plants, not they, are the unit of identity. Plant content gets buried in algorithmic Instagram and TikTok feeds, fragmented across Reddit and Discord with no continuity, or treated as inventory rows in care apps like Planta and Greg. No platform offers per-plant identity with continuity, a social loop driven by care logging, or a propagation lineage graph. The slot is empty, the behavior already exists (58% of millennial plant owners use social platforms to research plants), and incumbents could retrofit a personality layer within ~90 days of any visible launch.

# Capabilities

- id: CAP-1
  intent: A user creates, edits, archives, and sets visibility on a Plant Profile (name, species, photo, Personality Archetype) that is the social unit of identity on the platform.
  success: A user completes Plant Profile creation in one onboarding flow; the profile carries species, photo, Personality Archetype, follower count, post timeline, public Care Event Log summary, and Propagation Lineage links. Archive removes from public surfaces while preserving post history and lineage; no destructive delete is available.

- id: CAP-2
  intent: A user logs a Care Event and emits a Milestone Post in the plant's Voice as a single act, from a Composer reachable everywhere in the app.
  success: From the Composer, a user logs any v1 Care Event type (water, fertilize, light change, repot, prune, treat, propagate) with timestamp, optional notes, and optional photo, and produces a Milestone Post in the plant's Voice. Single-tap path confirms the log in under 1 second on a typical mobile connection; the post lands on the Plant Profile timeline and follower feeds, tagged with originating event type and Life Stage at emission.

- id: CAP-3
  intent: All Plant Profile posts and replies publish in a first-person Voice biased by Personality Archetype, with three composition modes (archetype-default, AI-assisted, manual) and a hard no-generic-output rule on AI-assisted outputs.
  success: AI-assisted outputs reference the Plant Profile's species, recent Care Event Log context, and Personality Archetype, OR return no suggestion at all. A test that feeds the AI-assisted path a prompt stripped of plant context produces silence, not a generic line. Manual mode is always available; archetype-default works with zero typing.

- id: CAP-4
  intent: Plant Profiles follow, like, reply to, and direct-message other Plant Profiles, in-character by default with an explicit out-of-character toggle for owner-to-owner coordination.
  success: A user can follow/unfollow another Plant Profile, like and in-character-reply to a Milestone Post (with the same three Voice modes as the Composer), and DM between two Plant Profiles. Block, mute, and report are available and route reports to moderation; in-character framing does not change moderation outcomes.

- id: CAP-5
  intent: A user explores Plant Profiles and posts outside their follow graph through a Discovery surface filtered by Cohort dimensions: species, metro (zip-cluster), and Life Stage.
  success: A new user's default Discovery view returns Cohort content matching their first Plant Profile's species and metro. Filters combine independently. Named cohort groupings appear ("3 other fiddle-leaf figs recovering from cold shock this week"). Geo is never finer than zip-cluster on any public surface.

- id: CAP-6
  intent: A user logs a propagation Care Event, generates Claim QRs (printable PDF or in-app display) for cuttings, and recipients scan to create or claim a child Plant Profile carrying a parent-link.
  success: Scanning a Claim QR opens the claim flow (account creation if needed) and creates a child Plant Profile pre-populated with parent species and a parent-link; the new owner picks Personality Archetype and name. Both parent and child emit Voice Milestone Posts on claim; the Propagation Lineage view shows transitive parent/child relationships with privacy-respecting display. PDF generation works without the recipient present.

- id: CAP-7
  intent: Scheduled prompts (daily check-in, weekly species-cohort digest, seasonal challenges, Photo of the Day) maintain feed activity between organic Care Events without crowding primary Home Feed content.
  success: Each injector publishes on its schedule and appears in Discovery and as opt-in notifications. Daily check-in delivers via push on native iOS and via email plus in-app banner on web/PWA. No injector content occupies a primary Home Feed position.

- id: CAP-8
  intent: A user views all owned Plant Profiles in one Care Hub surface with current Life Stage, days since last Care Event by type, and active health flags; reminders fire on species defaults overridable per-plant.
  success: ≥99.5% of scheduled reminders deliver to native iOS within the user-selected window. Quick-log from Care Hub records a Care Event without emitting a Milestone Post. Care Hub flags overdue care, anomalous patterns (e.g., repeated treat events), and upcoming seasonal transitions.

- id: CAP-9
  intent: A free tier carries all core social and care features; a premium subscription tier unlocks unlimited Plant Profiles, advanced Voice Tooling, per-Plant and aggregate analytics, and ad-free Discovery.
  success: A user upgrades to premium and gains higher AI-assist quotas, custom archetype tuning, the larger AI-assisted composer model, per-Plant-Profile analytics, retained full-resolution photo originals, and removal of Channel Partner Brand Profile content from primary feeds. Free-tier experience remains fully usable without dark-pattern downgrade.

- id: CAP-10
  intent: Channel Partners (nurseries, DTC retailers) create Brand Profiles, pre-create Plant Profiles in bulk for inventory, attach Claim QRs at point of sale, and view aggregate claim/post analytics. [v1.5, not launch-day v1]
  success: A customer buying from a partner nursery scans the pre-affixed Claim QR, claims a Plant Profile pre-populated with species, photo, and provenance ({partner name, location, date}), picks name and Personality Archetype, and publishes their first post within minutes of unboxing. Partner sees aggregate claim rate, post activity, and follower growth across their issued QRs.

- id: CAP-11
  intent: ~100 verified plant influencers in seed metros (Brooklyn, Austin, LA) migrate from Instagram with per-Plant-Profile analytics, exclusive Cohort access, and concierge support during launch.
  success: A concierge-cohort creator sees per-Plant-Profile analytics demonstrating audience density Instagram does not surface, and posts on Plantsona during the launch window at a cadence to be set with the cohort (see open_questions). The cohort's content sets the §6 Aesthetic and Tone bar referenced by archetype defaults.

# Constraints

- AI-assisted outputs (Composer and replies) must reference species, recent Care Event Log context, and Personality Archetype, or return no suggestion. A "generic but pleasant" output is not allowed.
- In-character framing is not a moderation shield. Reports treat in-character posts identically to out-of-character posts.
- Geo data is zip-cluster granularity on every public surface; EXIF location is stripped on photo upload; precise location is never surfaced publicly.
- Plant Profile post history is archive-only, not deletable, to preserve Propagation Lineage integrity across owners and time.
- v1 launch surface is Web responsive plus PWA universally, with native iOS in seed metros (Brooklyn, Austin, LA) only. No Android at launch. Care-reminder reliability target applies to native iOS; web/PWA reminders fall back to email plus in-app.
- All AI-assisted outputs pass pre-publication moderation (toxicity, harassment, spam, off-policy). Automated flags route to human-in-the-loop review.
- Cadence Injector content cannot displace organic Milestone Posts in primary Home Feed positions. Injectors live in Discovery and as opt-in notifications.
- Brand Profile content is clearly labeled, isolated from Home Feed primary positions, and subject to advertising-style standards distinct from user-generated content.
- Composer single-tap latency: under 1 second to log confirmation. AI-assisted suggestion latency: under 3 seconds, otherwise cancel and fall back to archetype-default.
- Free tier is rate-limited on AI-assist usage; full-resolution photo originals are retained only for premium-tier users.
- Minimum user age is 13 (US COPPA threshold) at launch; ToS-level age gate only.
- v1 is English-only; no internationalization is designed into string handling.

# Non-goals

- Not a care-tracking app with a community module. It is the inverse: a social network for plants with care tracking as the post-creation engine.
- Not a plant identification app. Integrate third-party ID APIs (PlantNet, PictureThis, equivalents); do not build proprietary ID.
- Not a marketplace. No transactions, escrow, or seller verification at v1.
- Not a general-purpose hobby social network. Plants only; pets, outdoor gardens, mushrooms, hydroponics-as-business are out.
- Not a wellness or mental health platform. Adjacency acknowledged, not the v1 voice or feature set.
- Not an advertising platform. No advertiser-facing tooling at launch.
- Not for commercial growers. No yield tracking, B2B inventory, or wholesale workflows.
- No native Android at launch (v2).
- No live or synchronous experiences. No live video, streaming, or synchronous chat — the milestone economy is asynchronous by design.
- No owner-transfer between human users at v1 (v2 candidate; archive-not-delete preserves the option).
- No internationalization beyond English at v1.

# Success signal

Within 14 days of seed-metro cohort onboarding (Brooklyn, Austin, LA), ≥60% of users hit the Liquidity Unit — at least one Plant Profile with 5 or more followers AND 3 or more in-character interactions — and ≥60% of Milestone Posts receive at least one in-character reply. That combined threshold is the demonstrable proof that per-plant identity plus care-as-post produces a self-sustaining social loop, and it gates broader geographic rollout.

# Assumptions

- A1. The v1 Personality Archetype set (Sassy, Anxious, Regal, Chaotic, Stoic) covers the v1 expressive palette. [PRD A1; linked OQ-12]
- A2. The v1 Life Stage taxonomy (establishing, healthy, recovering, blooming, propagating, dormant) covers v1 cases. [PRD A2; linked OQ-13]
- A3. Care Hub is a distinct top-level surface, not nested under Profile. [PRD A3]
- A4. Archive-not-delete is the correct v1 stance for Plant Profile history. [PRD A4]
- A5. PDF download is the dominant non-app Claim QR sharing path; in-app display covers in-person handoff. [PRD A5]
- A6. The $4.99–$7.99/mo premium band and 5–8% conversion target derive from Strava and Letterboxd comparables. [PRD A6; linked OQ-3]
- A7. ToS-level age gate (13+) is sufficient minor-user protection at launch. [PRD A7]
- A8. Free-tier rate limiting is sufficient AI inference cost control for v1. [PRD A8]
- A9. Free-tier photo compression is acceptable; originals retained for premium only. [PRD A9]
- A10. Monstera and Calathea are the right beachhead species for Cohort seeding. [PRD A10]
- A11. April–October seasonality holds for the bulk of the v1 user base. [PRD A11; linked OQ-9]
- A12 (distillation). The 44 PRD Functional Requirements consolidate into 11 kernel-scope capabilities; FR-level criteria are folded into each capability's `success` rather than enumerated 1:1. Capability IDs are stable from this distillation forward.

# Open questions

- OQ-1. Final brand name. Working title Plantsona; PRD §19 lists alternatives.
- OQ-2. Pre-MVP validation cohort (~50 plant collectors) before launch capital commit.
- OQ-3. Premium conversion benchmark for the plant hobby specifically (5–8% asserted, unvalidated).
- OQ-4. Posting cadence reality check against actual Greg/Planta care-log frequency.
- OQ-5. In-character voice durability past the novelty window (Petzbe analog data not public).
- OQ-6. Voice moat specificity: archetype expansion path, fine-tuning corpus, per-Plant voice memory, species-specific priors.
- OQ-7. Operational definition of the cringe/quality flywheel (seeded-creator bar, archetype tuning, moderation patterns).
- OQ-8. Launch marketing surface treatment of Propagation Lineage and Cohort Discovery (v1 features but secondary to milestone wedge).
- OQ-9. Southern-hemisphere recruitment and grow-light Cohort strategy for winter dormancy.
- OQ-10. Free-tier Plant Profile cap.
- OQ-11. Team size, capital, timeline (business plan, not spec — flagged for completeness).
- OQ-12. Final Personality Archetype set; validate in OQ-2 cohort.
- OQ-13. Life Stage taxonomy validation against actual Care Event signals.
- OQ-14. Brand Profile premium-tier eligibility (deferred to v1.5 Channel Partner spec).
- OQ-15 (new, surfaced by distillation). CAP-11 active-posting threshold for the concierge cohort during launch window — needs a concrete numeric floor to make the success criterion testable.
