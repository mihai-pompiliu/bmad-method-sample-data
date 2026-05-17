---
title: Plantsona
status: draft
created: 2026-05-11
updated: 2026-05-11
---

# PRD: Plantsona
*Working title. Final brand TBD; alternatives logged in `decision-log.md` and surfaced in §19.*

## 0. Document Purpose

This PRD is the v1 product specification for Plantsona, a per-plant social network. It is written for the PM (the document owner), for the downstream BMad workflow chain (UX, architecture, story creation), and for stakeholders who need a single source of truth on what v1 is and is not.

It builds on the brief distillate at `_bmad-output/planning-artifacts/briefs/plantsona-2026-05-01/distillate.md`. The distillate's content is treated as source of truth; this PRD elaborates it into capabilities, journeys, scope, and metrics. The brief is referenced, not duplicated.

A note on provenance: the source distillate was produced in headless mode from a one-paragraph concept, with specifics inferred from web research. The PM has confirmed the distillate's content as accurate; specific values still flagged here as `[ASSUMPTION]` (Personality Archetype set, Life Stage taxonomy, beachhead species, premium pricing band, free-tier policy) carry that provenance and warrant validation in pre-MVP testing (§19 OQ-2).

The document follows BMad PRD discipline: Glossary-anchored vocabulary, features grouped with Functional Requirements nested, capabilities not implementation, assumptions tagged inline and indexed, Non-Goals explicit, counter-metrics named. User journeys (UJ-N) and Functional Requirements (FR-N) carry globally stable IDs for downstream reference.

## 1. Vision

### 1.1 The Product

Plantsona is the place where plants live online. Every plant gets a profile, a voice, and a timeline. Owners post in-character ("Watered after 9 days. Worth the wait.") instead of grafting plant content onto a generic feed. Care tracking rides on top of the social loop instead of being bolted to the side of it.

Plant ownership is mainstream (market context in §2). Today plant owners' behavior is split across four broken tools: Instagram and TikTok bury posts in algorithmic feeds, care apps treat plants as inventory, Reddit and Discord lack identity and timeline, group texts fragment by recipient and lose history. Plant owners have nowhere to be plant owners. Plantsona gives them one.

### 1.2 The Three-Layer Thesis

Plantsona is built on a unified strategic claim, not a feature list. Three layers stack:

1. **Per-plant identity with continuity.** The plant, not the human, is the unit of identity. Each plant has a continuous public profile that persists across owner transfers, propagations, and years. No other platform offers this; it is a worldview shift in how a social product organizes people, not a tactic.
2. **Social-first stack with care tracking embedded.** Care logging is the post-creation engine; the social loop is the product surface. Inverts Planta and Greg's care-first-with-community-grafted-on architecture.
3. **Milestone economy plus propagation as distribution.** Strava-style activity logging mapped to plant life produces an organic content engine. Physical propagation (cutting plus QR plus claim) doubles as the viral loop and as the source of the parent-child plant pedigree graph that no competitor can replicate.

### 1.3 The Moat

Defensibility is execution plus accumulated longitudinal data, not a proprietary model. The moat compounds on two axes: voice tooling as operational discipline (Personality Archetype priors, per-Plant-Profile voice memory, the no-generic-output rule of FR-12) that competitors can match in principle but rarely in practice; and per-plant longitudinal identity (posts, Care Event history, follower context accumulated over years) that a 90-day retrofit cannot replicate. Time on platform compounds the moat each season.

## 2. Why Now

Algorithmic-feed fatigue is driving migration to identity-rich niche socials. Strava grew from 32M to 55M users since 2024. Letterboxd crossed 10M, also up more than 50%. Both succeed on the same pattern: a hobby with discrete events worth logging, an identity-first social graph, and asynchronous interaction tied to lived activity. Plants fit this pattern exactly and lack a product that claims the slot.

The market is large and growing: the US plant industry is approximately $21B and growing 9% per year. Two-thirds of US households own a houseplant. Seventy percent of millennials self-identify as plant parents and 41% of Gen Z plant owners self-identify as "collectors." Critically, 58% of millennial plant owners use social platforms (not care apps) to research plants today. The behavior exists; no product serves it.

Petzbe (~500K users) proves personification works at consumer scale and is a candidate cross-promotion partner. Greg's community is the strongest in plant care today and is reportedly "light years better than Planta's" per its users.

The risk in waiting: Greg or Planta could ship a personality layer onto their existing care apps within roughly 90 days of any visible launch. Plantsona's defense is the moat described in §1.3, neither side of which a care-app retrofit can match. That defense only holds if the product ships and reaches liquidity before incumbents react.

## 3. Three-Year Vision

By year three, Plantsona is the default identity layer for the plant hobby (the Letterboxd-for-film, Strava-for-running analog for plants). Three signals indicate the vision is realized:

- **Distribution.** Nurseries include a Plantsona QR on every plant tag. Buying a plant from any participating retailer comes with a pre-populated Plant Profile by default.
- **Influencer migration.** Plant influencers' primary handle moves from Instagram to Plantsona. Per-Plant-Profile analytics and audience density justify the migration that no other platform offers them.
- **Data product gravity.** Per-plant longitudinal data (Care Event history, environmental signals, milestone outcomes) becomes the most accurate real-world plant care dataset. Utility apps (PictureThis, PlantNet, Planta) want to integrate, not compete.

Monetization durability follows from identity stickiness: people do not leave the place where their plants live. After a year of timeline and follower history, the switching cost is the entire archive.

## 4. Competitive Landscape

The competitive map is intentionally explicit. Plantsona's positioning depends on naming what each existing player does well, what they leave open, and where Plantsona fits.

- **Greg.** Strongest community in the plant-care category. Could ship a personality layer within ~90 days of Plantsona's launch becoming visible; assume they will. Plantsona's defense: see §1.3.
- **Planta.** Premium AI care, large installed base, paywalled core features. Strength is reminder quality and care logic. Weakness is community. Plantsona must match Planta's reminder quality (the forcing function for retention; see §9.7) while owning the social surface Planta has not built.
- **Petzbe (~500K).** Personification proof point at consumer scale and a likely ceiling signal (the pet category is larger than plants; if Petzbe caps below 1M, the plant equivalent caps lower). Potential cross-promotion partner. Not a direct competitor.
- **Reddit r/houseplants (~2M) and plant Discords.** The actual incumbents users would defect from. Entrenched, free, well-aligned for troubleshooting and show-and-tell. Not broken. Switching trigger cannot be "we have community"; it must be "we have your plants' identity and timeline." Per-Plant-Profile continuity is the wedge against Reddit and Discord, not raw community size.
- **PictureThis, PlantNet, Pl@ntNet, Blossom.** ID-and-care utilities with no social graph. Integration partners, not competitors. Plantsona uses their APIs; Plantsona does not build proprietary plant ID (§14 Non-Goals).
- **Instagram and TikTok.** Where plant content currently lives by default and where it gets buried. Plantsona pulls plant identity off algorithmic-feed platforms into a dedicated identity-first surface. Not a head-to-head competitor; the hypothesis is parallel use (post on both, identity on Plantsona) shifting toward Plantsona-primary as plant collections accumulate longitudinal value.

The structural opportunity is that no existing player owns the per-plant identity slot. Greg and Planta own care; Reddit and Discord own troubleshooting community; Petzbe owns pet personification. The plant-personification slot is empty and the bar to enter is low. Plantsona is walking into a vacuum, not contesting a held position.

## 5. Target User

### 5.1 Primary Persona: The Collector

Twenty-five to forty years old, owns five to fifty or more plants (the 30-plus segment is the premium-tier target), spends $20 to $80 a month on plants, posts plant content on Instagram today, and identifies as a plant person to friends and online. Forty-one percent of Gen Z plant owners self-identify as "collectors," indicating the segment has scale and is not a millennial-only phenomenon. The collector is comfortable with social product mechanics, frustrated that no platform centers their hobby, and willing to migrate identity to a product that delivers per-plant continuity. This is the engagement engine and the premium revenue base.

### 5.2 Secondary Persona: The New Plant Parent

One to two plants, intimidated by care, currently using a free care app or Google searches to troubleshoot. The character voice softens the troubleshooting curve: the plant tells you what it needs instead of a care app pushing schedules. Picking a Personality Archetype on first onboarding is a low-stakes creative onboarding moment; it produces immediate emotional investment. The new parent is the funnel widener and the long-tail organic content source.

### 5.3 Channel Partner (v1.5): Nurseries and DTC Retailers

Independent and DTC nurseries in seed metros pre-create Plant Profiles for inventory, attach Claim QRs at point of sale, and run co-branded Brand Profiles on the platform. This is the shortest path to liquidity in seed metros and the foundation of post-launch nursery distribution.

### 5.4 Plant Influencer (Concierge cohort)

Verified plant content creators currently posting to Instagram and TikTok. Per-Plant-Profile analytics, exclusive Cohort access, and a path to direct audience monetization differentiate Plantsona from Instagram (which offers none of these to plant creators). Concierge Onboarding for ~100 of these is a v1 launch initiative.

### 5.5 Non-Users (v1)

- **Plant ID seekers.** Users who primarily want to identify unknown plants from photos. Plantsona integrates existing ID services as utility, not a feature pillar; users wanting ID as the core experience belong on PictureThis or PlantNet.
- **Commercial growers and horticulture professionals.** Plantsona is hobbyist-first; commercial workflows (yield tracking, business inventory, B2B marketplace) are not addressed in v1.
- **Outdoor garden hobbyists.** v1 centers indoor and patio plants. Outdoor gardening has different cadence (seasonal planting beds, vegetable rotations) and is a poor fit for the milestone-driven feed at launch. [v2 candidate]

### 5.6 Jobs To Be Done

- **Express plant identity publicly.** "I want my plants to have a place to be themselves, not be hidden inside my Instagram grid."
- **Record care without it feeling like data entry.** "I want care logging to feel like posting, not like spreadsheet maintenance."
- **Connect with people growing the same plants.** "I want to find the other people whose Calathea is also throwing a tantrum this week."
- **Celebrate milestones.** "When my fiddle leaf finally throws a new leaf, I want that moment to land somewhere that cares."
- **Soften the care learning curve.** (New parent) "I want my plant to tell me what it needs in language I understand."
- **Document propagation lineage.** "I gifted three cuttings to friends and I want to know what happened to them."

### 5.7 Key User Journeys

Named flows the product enables. Detailed flow design (steps, screens, edge flows) is the responsibility of the downstream UX workflow. Features in §9 reference these by ID.

- **UJ-1. First plant onboarding.** New user creates an account, adds their first plant, picks a Personality Archetype, lands in their first feed view with seeded relevant Cohort content.
- **UJ-2. Daily care-as-post.** User logs a Care Event in the Composer, the plant emits a Milestone Post in its Voice, the post is visible to followers. Single-tap path is the dominant case.
- **UJ-3. Same-species discovery.** User finds the Cohort for their species and Life Stage, follows other Plant Profiles, replies in-character, sees their feed populate without effort.
- **UJ-4. Propagation give and claim.** User logs a propagation event, prints or receives a Claim QR for the cutting, gifts the cutting, recipient scans the QR, child Plant Profile is created and parent-link is established.
- **UJ-5. Milestone moment.** A plant hits a major milestone (new leaf, first bloom, full recovery, repot graduation). The Milestone Post lands in followers' feeds with appropriate weight; followers respond in-character.
- **UJ-6. Nursery purchase to first post.** Customer buys a plant from a partner nursery with a pre-affixed Claim QR, scans, claims the Plant Profile (already populated with species, photo, and provenance), publishes their first post within minutes of unboxing.
- **UJ-7. Dormancy and seasonal return.** Northern-hemisphere user's engagement tapers in winter (April-October is the heavy use window for the bulk of the user base); product holds them with seasonal Cohort content (overwintering, low-light recovery) and welcomes them back in spring with species-cohort milestones.
- **UJ-8. Concierge creator onboarding.** Verified plant influencer migrates from Instagram, populates their collection, gets per-Plant-Profile analytics that demonstrate audience ROI Instagram cannot show, has a path that justifies posting on Plantsona instead of (or alongside) Instagram.

## 6. Aesthetic and Tone

Plantsona's voice is the product. Get it wrong and the platform reads as cringe; get it right and it reads as warm, joyous, and fun. Petzbe has proven this bar is achievable. The aesthetic and tone rules below are part of the v1 specification (not deferred to UX or copywriting) because they govern Voice Tooling outputs and seed-creator content.

**Tone target.** Warm. Joyous. Specific. Non-cringe. The bar is "the plant feels alive and present," not "the plant is performing for an audience."

**Voice principles** (apply to all in-character text, archetype defaults, AI-assisted composer outputs).

- **Specific over generic.** "Worth the wait" beats "feeling great today." The plant references its own situation, not a stock plant sentiment.
- **Restrained anthropomorphism.** Plants have feelings, opinions, and observations. They do not have human jobs, romantic relationships, or political opinions.
- **Sensory and seasonal.** Light, water, temperature, season, and physical change are the texture. The voice draws from what the plant actually experiences.
- **Funny when funny lands, sincere otherwise.** Humor is a tool, not the brand. A bloom post that is sincerely awed beats a forced joke.

**Anti-references** (what to avoid).

- LinkedIn-style faux profundity. ("Today my fiddle leaf taught me about resilience.")
- Pet-style baby-talk that slides into infantilization. (Petzbe walks this line; Plantsona stays drier.)
- Generic AI-slop sentiment posts. The AI-assisted composer must produce specific, plant-grounded outputs or remain silent.
- Wellness-industrial-complex framing as the dominant note. Mental-health adjacency is a real value vector (see §18 Adjacent Value), but it is not the v1 voice.

**Personality Archetypes (v1 set).** Sassy, Anxious, Regal, Chaotic, Stoic. Picked once at Plant Profile creation, refinable later. Each archetype is a tone preset that biases the AI-assisted composer and provides default response templates. [ASSUMPTION: this five-archetype set is the right v1 starting palette; refinement and possible expansion is a v1.5 question.]

## 7. Glossary

Downstream workflows and readers must use these terms exactly. Synonyms produce drift.

- **Plant Profile.** A per-plant identity record. Has a name, species, photo, Personality Archetype, Care Event Log, follower graph, and post timeline. One human user owns many Plant Profiles. A Plant Profile may have one parent Plant Profile (via propagation) and many children. Plant Profiles persist across owner transfer events. [v2: owner transfer is post-MVP.]
- **Voice (or In-Character Voice).** The first-person mode in which a Plant Profile publishes posts and replies. All Plant Profile content in the public feed is in Voice; account-level content (settings, notifications) is in the human user's voice.
- **Personality Archetype.** A Voice tone preset. v1 set: Sassy, Anxious, Regal, Chaotic, Stoic. Picked at Plant Profile creation, refinable.
- **Care Event.** A discrete logged care action on a Plant Profile. v1 event types: water, fertilize, light change, repot, prune, treat (pest or disease), propagate. Each Care Event has timestamp, type, optional notes, and optional photo.
- **Care Event Log.** The event-sourced sequence of Care Events for a Plant Profile. Source of truth for care history; aggregates feed both private history views and Cohort-level analytics.
- **Composer.** The surface where a user logs a Care Event and produces (or skips) a corresponding Milestone Post. Single-tap default path; archetype, AI-assisted, and manual Voice modes available.
- **Milestone Post.** A public post emitted by a Plant Profile in Voice. Most Milestone Posts originate from Care Events in the Composer; some originate from passive milestones (e.g., follower count thresholds, anniversaries).
- **Cohort.** A grouping of Plant Profiles by shared attributes. v1 Cohort dimensions: species, metro (zip-cluster), Life Stage. Used for Discovery feeds and Cadence Injectors.
- **Life Stage.** A Plant Profile's current state. v1 Life Stages: establishing, healthy, recovering, blooming, propagating, dormant. [ASSUMPTION: this six-state set covers the v1 cases.] Inferred from Care Event Log signals; user-overridable.
- **Propagation Lineage.** The directed graph of parent-to-child Plant Profile relationships established by propagation events. Persists across owner transfer.
- **Claim QR.** A QR code that, when scanned, creates a Plant Profile (or claims a pre-created one) and establishes parent-link if the QR was generated from a propagation event.
- **Cadence Injector.** A scheduled prompt or content surface that maintains feed activity between organic events. v1 set: daily check-in, weekly species-cohort digest, seasonal challenge, Photo of the Day.
- **Discovery Feed.** A filterable feed surface (separate from the Home Feed) showing posts and Plant Profiles outside the user's follow graph, filtered by Cohort dimensions.
- **Liquidity Unit.** The metric defining whether the social graph has reached self-sustaining density: percentage of users whose Plant Profile has 5 or more followers AND 3 or more in-character interactions within 14 days of joining.
- **Brand Profile.** A Plant Profile variant owned by a nursery, retailer, or other commercial entity. Distinguishable in UI. Premium-tier eligibility and Channel Partner monetization terms are deferred to v1.5 spec.
- **Channel Partner.** A nursery, DTC retailer, or commercial entity participating in the v1.5 nursery onboarding program.
- **Concierge Onboarding.** High-touch onboarding for verified plant influencers (verification, custom analytics, content support) during launch.
- **Voice Tooling.** The collection of features producing in-character text: archetype defaults, AI-assisted composer, manual editing.

## 8. Information Architecture

Top-level surfaces in the v1 product.

- **Home Feed.** Posts from Plant Profiles the user follows, ordered chronologically with milestone weighting. Default landing surface for returning users.
- **Discovery.** Filterable feed by Cohort dimensions (species, metro, Life Stage). Includes seeded Cadence Injector content and species-cohort digests.
- **Plant Profile.** A single Plant Profile's timeline, Care Event history (private to owner unless shared per-event), Propagation Lineage view, and follower graph.
- **Composer.** Care Event logging plus Milestone Post production. Reachable from anywhere (persistent action button) and from Home Feed.
- **Care Hub.** The user's collection of owned Plant Profiles in a single view, with care reminders, health flags, and quick-log access. The "care app" surface that holds the Planta/Greg parity. The social loop is primary; Care Hub is the parity floor for users in low-engagement periods (winter dormancy, dropoff months) and the utility-tier safety net.
- **Notifications.** Replies, follows, milestone reactions, propagation-claim events, care reminders.
- **Profile (User-level).** The human user's account: settings, owned Plant Profiles index, premium-tier status, subscriptions to other users.
- **Brand Profile (Channel Partner v1.5).** Inventory-style view for nurseries with attached Claim QRs and analytics.

[ASSUMPTION: Care Hub as a distinct surface (rather than nested under Profile) is the right call. It matches Planta/Greg's care-app feel for users who need that affordance, while the Composer's persistent presence preserves the social-primary IA.]

## 9. Features

Each subsection describes one feature: behavior first, Functional Requirements nested, then any feature-specific NFRs and notes. FRs are numbered globally; user journeys are referenced by ID.

### 9.1 Plant Profile and Voice

A user creates a Plant Profile by adding a name, species (typed or selected from an integrated plant ID search), an initial photo, and a Personality Archetype. Voice is set immediately and applied to all subsequent in-character outputs. Plant Profile creation is the foundational act of using the product (realizes UJ-1, UJ-6).

**Functional Requirements:**

- **FR-1.** A user can create a Plant Profile with name, species, photo, and Personality Archetype in a single onboarding flow.
- **FR-2.** A user can edit a Plant Profile's name, photo, and Personality Archetype at any time after creation.
- **FR-3.** A user can mark a Plant Profile's species using either typed entry, integrated plant-ID search (via third-party API), or selection from a curated species list.
- **FR-4.** A Plant Profile displays its Voice (Personality Archetype), species, owner's display name, follower count, Care Event Log summary (public surface), Propagation Lineage links, and post timeline.
- **FR-5.** A user can set per-Plant-Profile visibility (public, followers-only, private).
- **FR-6.** A Plant Profile owner can rename or archive a Plant Profile but cannot delete its post history once published; archive removes from public surfaces and preserves history. [ASSUMPTION: archive-not-delete is the right v1 stance for preserving Propagation Lineage integrity.]

**Notes:** Owner transfer between human users (gift, breakup, death) is a v2 feature. [v2: out of MVP] [NOTE FOR PM: high emotional weight; preserve archive-not-delete to keep lineage intact when v2 lands.]

### 9.2 Composer and Care Logging (the wedge)

The Composer is the v1 narrative spine. It collapses care logging and social posting into one act: a Care Event is logged, the plant emits a Milestone Post in its Voice, the post lands in the Home Feed. The single-tap default path is the dominant case; archetype-default and AI-assisted modes accelerate it; manual mode is always available (realizes UJ-2, UJ-5).

**Functional Requirements:**

- **FR-7.** A user can log a Care Event (water, fertilize, light change, repot, prune, treat, propagate) on a Plant Profile in a single tap from the Composer or Care Hub.
- **FR-8.** Each Care Event captures timestamp, type, optional notes, and optional photo.
- **FR-9.** The Composer offers three Voice modes for the resulting Milestone Post: archetype-default (no editing required), AI-assisted (suggested text the user can edit), and manual (free composition).
- **FR-10.** The user can configure visibility per Milestone Post (public, followers-only, suppressed: care logged but no post emitted).
- **FR-11.** Milestone Posts are tagged with their originating Care Event type and Plant Profile Life Stage at time of emission.
- **FR-12.** The Composer suppresses generic AI output: AI-assisted text must reference the Plant Profile's species, recent Care Event Log context, and Personality Archetype. If the model cannot produce specific output, it returns no suggestion rather than a generic one.
- **FR-13.** A user can mark a Care Event as a major milestone (new leaf, first bloom, full recovery, repot graduation), which gives the corresponding Milestone Post elevated weight in the Home Feed and triggers richer follower notifications.
- **FR-14.** The Care Event Log for a Plant Profile is queryable in chronological order with filters by event type and date range.

**Feature-specific NFRs:**

- Composer single-tap path latency target: under 1 second from tap to confirmed log on a typical mobile network.
- AI-assisted suggestion latency target: under 3 seconds for the first suggestion; longer cancels and falls back to archetype-default.

**Notes:** Voice quality is the moat (§1.3). The discipline in FR-12 (no generic output ever) is non-negotiable; failure here erodes the §6 Aesthetic and Tone bar and pushes the brand into the AI-slop anti-reference.

### 9.3 Social Graph and Interactions

Plant Profiles follow Plant Profiles. Likes are likes. Replies are in-character. Direct messages between Plant Profiles are also in-character (with an out-of-character mode for owner-to-owner coordination). The graph is plant-to-plant; the human user is the operator, not the social entity.

**Functional Requirements:**

- **FR-15.** A user can follow and unfollow another Plant Profile from any Plant Profile surface or post.
- **FR-16.** A user can like a Milestone Post.
- **FR-17.** A user can reply to a Milestone Post in-character, with the same three Voice modes available as in the Composer (archetype-default, AI-assisted, manual).
- **FR-18.** A user can send a direct message between two of their Plant Profiles and any other Plant Profile they follow, in-character by default with an explicit out-of-character toggle.
- **FR-19.** Users can block, mute, and report Plant Profiles. Reports are routed to moderation (see §13).
- **FR-20.** Follow recommendations are surfaced based on Cohort overlap (species, metro, Life Stage) and friend-of-friend graph signals.

### 9.4 Discovery

The Discovery Feed makes the product feel populated even when the user follows few Plant Profiles. It is the primary onboarding-to-engagement path (realizes UJ-3, UJ-7) and the main defense against the empty-room death spiral.

**Functional Requirements:**

- **FR-21.** A user can browse Discovery filtered by species, metro (zip-cluster), and Life Stage independently or in combination.
- **FR-22.** Default Discovery view for a new user shows Cohort content matching the species and metro of their first Plant Profile.
- **FR-23.** Cadence Injector content (daily check-in prompt, weekly species-cohort digest, seasonal challenge, Photo of the Day) appears in Discovery in addition to the Home Feed.
- **FR-24.** Discovery surfaces same-species, same-Life-Stage cohorts as named groupings ("3 other fiddle-leaf figs recovering from cold shock this week").
- **FR-25.** Geo data in Discovery is zip-cluster granularity only; precise location is never surfaced publicly.

### 9.5 Propagation and Claim QR

Propagation is the platform's distribution loop and the long-term moat asset (the Propagation Lineage graph). A user logs a propagation event, generates a Claim QR for the cutting, gifts the cutting, the recipient scans the QR, and a child Plant Profile is created with the parent-link established (realizes UJ-4).

**Functional Requirements:**

- **FR-26.** A user can log a propagation Care Event from a Plant Profile, generating one or more Claim QRs (one per cutting).
- **FR-27.** A Claim QR is printable (PDF download) or displayable (in-app screen) for sharing.
- **FR-28.** Scanning a Claim QR opens a claim flow: existing user accounts proceed directly to claim; new users complete account creation and then claim.
- **FR-29.** Claiming a Claim QR creates a child Plant Profile pre-populated with parent species and a parent-link reference; the new owner picks Personality Archetype and name.
- **FR-30.** Both parent Plant Profile owner and child Plant Profile owner are notified on claim, and both Plant Profiles emit Milestone Posts on claim (in their respective Voices).
- **FR-31.** A Plant Profile's Propagation Lineage view shows all parent and child relationships transitively, with privacy-respecting display (children opted-out of public lineage are anonymized in the parent's view).

**Notes:** Claim QR generation must work offline (PDF download path) so propagation works without forcing the giver to be at the moment of gifting. [ASSUMPTION: PDF download is the dominant non-app sharing path; in-app display is the in-person sharing path.]

### 9.6 Cadence Injectors

Cadence Injectors are required infrastructure: care events are naturally weekly to monthly, but the feed must feel daily. Without injectors, the Home Feed and Discovery look empty for users with low follow counts and the Liquidity Unit is unreachable.

**Functional Requirements:**

- **FR-32.** A daily check-in prompt is delivered to the user (push notification on iOS, email and in-app banner on web/PWA) inviting a single Care Event log or a Photo of the Day post.
- **FR-33.** A weekly species-cohort digest is published per species per metro, surfacing notable Milestone Posts and active Plant Profiles in the user's Cohort.
- **FR-34.** Seasonal challenges (overwintering check-in, spring repot week, summer growth photos) are scheduled and promoted in Discovery and via notification.
- **FR-35.** A Photo of the Day prompt is published daily (per species or per metro variant) and submitted Photos of the Day surface in Discovery.

**Feature-specific NFRs:**

- Cadence Injector content must not displace organic Milestone Posts in the Home Feed; injectors live in Discovery and as opt-in notifications, not as Home Feed primary content.

### 9.7 Care Hub and Reminders

The Care Hub is the parity surface for Planta and Greg-level care utility (realizes UJ-7). The forcing function is load-bearing: in winter, when the social loop empties for the bulk of the user base, care utility is what holds users through the dormant period. Reminder quality cannot be a downgrade from Planta or Greg; if it is, dormant-period retention collapses.

**Functional Requirements:**

- **FR-36.** A user can view all their owned Plant Profiles in a single Care Hub surface, with current Life Stage, days since last Care Event by type, and any active health flags.
- **FR-37.** Care reminders are scheduled per Plant Profile based on species defaults (overridable per Plant Profile) and surfaced via push notification (iOS native), email and in-app (web/PWA).
- **FR-38.** A user can quick-log a Care Event from the Care Hub without entering the Composer (skips Milestone Post emission; logged as suppressed-visibility Care Event).
- **FR-39.** The Care Hub flags Plant Profiles with overdue care, anomalous Care Event patterns (e.g., repeated treat events suggesting persistent pest issue), or upcoming seasonal transitions.

**Feature-specific NFRs:**

- Care reminder reliability target: 99.5% of scheduled reminders delivered within the user's selected window. [NOTE FOR PM: the iOS PWA push limitation (see §10) means web/PWA users get email plus in-app reminders only; push notification reliability target applies to native iOS only. This is a known retention vulnerability for the web/PWA cohort and is the primary driver to expand native iOS rollout post-v1.]

### 9.8 Channel Partner (Nursery) Onboarding (v1.5)

Nursery and DTC retailer onboarding is the shortest path to seed-metro liquidity and the foundation of post-launch distribution. v1.5 (not launch-day v1) so the consumer product proves liquidity first; the tooling is built and seeded in select partnerships during launch.

**Functional Requirements:**

- **FR-40.** A Channel Partner can create a Brand Profile, distinct in UI from a hobbyist Plant Profile, with retailer name, logo, location, and inventory listing.
- **FR-41.** A Channel Partner can pre-create Plant Profiles in bulk (CSV import or admin UI) attached to inventory SKUs.
- **FR-42.** A Channel Partner can generate Claim QRs in bulk for pre-created Plant Profiles, suitable for printing on plant tags affixed at point of sale.
- **FR-43.** When a customer scans a Channel-Partner-issued Claim QR, the resulting Plant Profile is pre-populated with species, photo, and provenance (purchase from {partner name, location, date}); the customer picks a name and Personality Archetype to complete claim.
- **FR-44.** A Channel Partner sees aggregate analytics on claimed Plant Profiles: claim rate, post activity, follower growth across their issued QRs.

## 10. Platform

**v1 launch surface.** Web responsive plus PWA shipped to all users; native iOS shipped concurrently in seed metros (Brooklyn, Austin, LA); Android deferred to v2. Web/PWA gives the lowest-friction Claim QR path (scan, claim, post within minutes without app store friction), which is critical to the propagation loop and the nursery onboarding pilot. Native iOS in seed metros covers the collector demographic where push reminder reliability is load-bearing for retention.

**Acknowledged trade.** iOS PWA push notifications are throttled; care reminder fidelity for web/PWA-only users is degraded relative to Planta and Greg's native apps. iOS native users get full push; web/PWA users get email plus in-app reminders. This is a known weakness of the v1 surface mix and the primary driver for Android (v2) and broader native iOS (post-v2).

**Distillate override.** The source distillate left web-vs-iOS as an Open Question. This PRD closes it with the configuration above; logged in `decision-log.md`.

**v2 platform expansion.** Native Android, broader native iOS beyond seed metros, and any native iOS features the seed-metro experience proves necessary.

[NON-GOAL for MVP] Desktop-native applications. Plantsona is mobile-first by design; web responsive serves desktop users adequately for v1.

## 11. Monetization

**v1 model.** Free tier with all core social and care features. Premium subscription tier at a target price point of $4.99 to $7.99 per month. [ASSUMPTION: this band is correct based on Strava (~$12/mo, ~12% conversion) and Letterboxd (~$5/mo, ~7% conversion) comparables; Plantsona conversion target 5-8% is unproven and surfaced as Open Question OQ-3.]

**Premium tier features (v1 candidates):** unlimited Plant Profiles (free tier capped at a number TBD; archives do not count against the cap), advanced Voice Tooling (custom archetype tuning, larger model for AI-assisted composer), per-Plant-Profile and aggregate care analytics, ad-free Discovery (no Channel Partner Brand Profile content in primary feeds).

**v1.5 revenue: Channel Partner (nursery) partnerships.** Co-branded Brand Profiles, paid placement in Discovery (clearly labeled, not in Home Feed), and SKU-attached Claim QR programs. Revenue model and Brand Profile premium-tier eligibility deferred to v1.5 Channel Partner spec.

Advertiser tooling and marketplace transactions are explicit non-goals for v1; see §14 for the full statement and §17 R7 for the trust-and-safety implications.

## 12. Cross-Cutting NFRs

System-wide quality attributes not tied to a single feature.

- **Performance.** Home Feed initial load target under 2 seconds on a typical mobile connection. Composer single-tap care log under 1 second to confirmation (matches FR-7 NFR). Discovery feed scroll target 60 frames per second on mid-tier mobile.
- **Reliability.** 99.9% uptime target for core surfaces (Home Feed, Composer, Plant Profile, Care Hub). Notification delivery: 99.5% for native iOS push reminders within selected window.
- **Observability.** All Care Event captures, Milestone Post emissions, follow events, and Claim QR scans are instrumented. Liquidity Unit is computable as a live metric.
- **Scalability.** Architecture must accommodate v1 success metrics (100K registered Plant Profiles by month 6, 500K by month 12) without re-architecture. Capacity model is the responsibility of the architecture workflow.
- **Accessibility.** WCAG 2.1 AA conformance target for all primary surfaces. Voice modes and Care Event logging usable with screen readers.
- **Localization.** v1 is English-only. Internationalization is post-v2 and explicitly not designed for in v1 string handling. [v2 candidate]

## 13. Constraints and Guardrails

### 13.1 Safety and Trust

- **AI-generated content moderation.** AI-assisted composer outputs and AI-assisted reply outputs run through pre-publication moderation (toxicity, harassment, spam, off-policy content). Humans-in-the-loop for any content flagged by automated moderation but not auto-removed.
- **In-character harassment.** In-character framing is not a shield. Reports treat in-character posts identically to out-of-character posts for moderation purposes.
- **Channel Partner content boundaries.** Brand Profile content is clearly labeled, isolated from Home Feed primary content, and subject to advertising standards distinct from user-generated content.
- **Minor users.** v1 minimum age is 13 (US COPPA threshold). [ASSUMPTION: terms-of-service age gate is sufficient at launch; deeper minor-user protections (parental consent flows, additional content filtering) are post-launch as user base demographics emerge.]

### 13.2 Privacy

- **Geo data.** Zip-cluster granularity for Cohort and metro Discovery features. Precise location is never surfaced publicly. Precise location captured by photo EXIF is stripped on upload.
- **Care Event Log privacy.** Care Event Log contents are private to the Plant Profile owner by default. Per-Care-Event visibility (visible on the originating Milestone Post, or suppressed) is user-controlled.
- **Cross-Plant-Profile inference.** Care patterns and engagement patterns are not surfaced or sold in aggregate forms that enable de-anonymization of individual users. (Aggregate climate-cohort data product is a v1.5 / v2 candidate; see §18, requires explicit opt-in.)
- **Data export.** Users can export their full account data (Plant Profiles, Care Event Logs, post history) on request. Required for GDPR-style compliance even where not strictly applicable; a default trust commitment.

### 13.3 Cost

- **AI inference cost.** AI-assisted composer is the largest variable cost driver, so free tier is rate-limited; premium tier gets higher quotas and the larger model. [ASSUMPTION: rate limiting on free tier is sufficient cost control for v1; revisit if free-tier inference cost becomes material.]
- **Storage.** Photo storage scales with Plant Profile count and Care Event capture rate. Photos are compressed on upload; full-resolution originals are retained for premium users only. [ASSUMPTION: free-tier compression is acceptable for the social-feed use case; premium-tier originals serve power users and any future analytics needs.]

## 14. Non-Goals (Explicit)

This product is not, and in v1 will not become:

- **A care-tracking app with a community module.** It is the inverse: a social network for plants, with care tracking as the post-creation engine. The framing is load-bearing; deviating drifts toward Planta-with-comments.
- **A plant identification app.** Integration with existing ID services (PlantNet, PictureThis, or equivalents) is the v1 stance. Building proprietary ID is not on the roadmap.
- **A plant marketplace.** Transactions, escrow, and seller verification are absent from v1. Revisit only after care-history-as-provenance is proven as a buyer trust signal.
- **A general-purpose hobby social network.** Plantsona is for plants. Pets, gardens of the outdoor variety, mushroom cultivation, hydroponics-as-business, and so on are not v1. (Some adjacencies, particularly outdoor gardens, are v2 candidates.)
- **A wellness or mental health platform.** The wellness adjacency is acknowledged in long-term Adjacent Value (§18) but is not the v1 brand voice or feature set.
- **An advertising platform.** No advertiser-facing tooling at launch. Channel Partner Brand Profile placement is clearly labeled and isolated from Home Feed.
- **A platform for commercial growers.** Commercial workflows (yield tracking, B2B inventory, wholesale) are out of scope.
- **An Android-native experience at launch.** v1 is web/PWA plus native iOS in seed metros; Android comes in v2.
- **A live or synchronous social experience.** Live video, live streaming, and synchronous chat are out of v1. The milestone economy is asynchronous by design.

## 15. MVP Scope

### 15.1 In Scope (v1 launch)

- Plant Profile creation, editing, archiving, visibility controls (FR-1 through FR-6).
- Voice modes: archetype-default, AI-assisted (with no-generic-output discipline), manual (FR-9, FR-12).
- Care Event logging across all v1 event types: water, fertilize, light, repot, prune, treat, propagate (FR-7, FR-8).
- Composer with single-tap path and three Voice modes (FR-7 through FR-14).
- Social graph: follow, unfollow, like, in-character reply, in-character DM with out-of-character toggle (FR-15 through FR-20).
- Discovery Feed with species, metro, Life Stage filters; default Cohort view for new users (FR-21 through FR-25).
- Propagation logging, Claim QR generation (printable and in-app), claim flow, parent-link establishment, Propagation Lineage view (FR-26 through FR-31).
- Cadence Injectors: daily check-in, weekly species-cohort digest, seasonal challenges, Photo of the Day (FR-32 through FR-35).
- Care Hub with care reminders (push on iOS native, email plus in-app on web/PWA), quick-log, health flags (FR-36 through FR-39).
- Web responsive plus PWA on all platforms; native iOS in seed metros (Brooklyn, Austin, LA).
- Premium subscription tier (free vs. paid feature gating per §11).
- Concierge Onboarding for ~100 plant influencers in seed metros.
- Beachhead species focus: Monstera and Calathea Cohorts seeded with high-quality content. [ASSUMPTION: these two species offer the right combination of large existing online community and identifiable troubleshooting content depth.]

### 15.2 In Scope (v1.5, post-launch within first ~6 months)

- Channel Partner (nursery) onboarding tooling: Brand Profile creation, bulk Plant Profile creation, bulk Claim QR generation, Channel Partner analytics (FR-40 through FR-44).
- Five DTC and independent nursery pilots in seed metros.

### 15.3 Out of Scope for v1

- Native Android. [v2]
- Owner transfer between users (gift, breakup, death). [v2; high emotional weight, see §9.1 Notes.]
- Advertiser tooling and broad ad-supported model. [v2 candidate]
- Marketplace transactions. [v2 candidate; gated on care-history-as-provenance proof.]
- Plant ID as a feature pillar (integration only, not pillar). [Permanent: see §14 Non-Goals.]
- National launch. v1 is seed-metro-concentrated by design; broader rollout is v2.
- Live video and synchronous chat. [Permanent: see §14 Non-Goals.]
- Internationalization beyond English. [v2 candidate]
- Outdoor garden, mushroom, and adjacent hobby support. [v2 candidate]

## 16. Success Metrics

Length and rigor scale with the consumer-venture stakes. Counter-metrics matter as much as primary metrics; they prevent the architect, the dev, and the growth team from optimizing the wrong target.

### 16.1 Primary Metrics

- **Liquidity Unit (gating).** ≥60% of seed-metro users hit the Liquidity Unit (5+ followers AND 3+ in-character interactions on a Plant Profile within 14 days of joining). This metric gates scale-up; if not met, fix before broadening geography.
- **In-character interaction rate.** ≥60% of Milestone Posts receive at least one in-character interaction.
- **DAU/MAU.** ≥0.30 across the active user base post-liquidity. Hobby-anchored social benchmark.
- **Retention.** 30-day retention ≥40%; 90-day retention ≥25%.

### 16.2 Secondary Metrics

- **Plant Profile density.** Median user maintains 3 or more Plant Profiles by 60 days post-signup.
- **Posting cadence.** Median user posts 4 or more times per Plant Profile per month, post-liquidity.
- **Registered Plant Profiles.** 100K by month 6, 500K by month 12. Gated on Liquidity Unit threshold being met first.
- **Premium conversion (year 2).** 5-8% of monthly active users on premium tier. [ASSUMPTION: see §11 and OQ-3.]
- **Claim QR redemption rate.** ≥40% of generated Claim QRs claimed within 60 days of generation. (Measures propagation distribution efficacy.)
- **Seasonal engagement profile.** April through October is the heavy use window for the Northern-hemisphere user base; track DAU/MAU and posting cadence by month to surface dormancy depth and inform §18 grow-light-cohort planning. [ASSUMPTION: April-October seasonality is correct for the bulk of the v1 user base.]

### 16.3 Counter-Metrics (Do Not Optimize)

- **Total registered Plant Profiles ahead of Liquidity Unit threshold.** Optimizing growth before liquidity produces an empty room and accelerates the death spiral. Hold the line; pre-liquidity Plant Profile growth is not a launch goal.
- **AI-assisted composer usage rate.** Heavy AI assist usage that crowds out manual or archetype-default Voice degrades the no-generic-output discipline (FR-12) and erodes the §6 Aesthetic and Tone bar. Watch for over-reliance, do not target growth.
- **Posts per active user per day in absolute terms.** The natural cadence is multiple posts per week per Plant Profile, not daily per user. Optimizing for daily-post volume produces feed spam and forces Cadence Injectors to displace organic Milestone Posts.
- **Time spent in app.** Plantsona is asynchronous by design. Time-spent maximization pushes toward attention-extraction patterns that conflict with the brand. Track for product health; do not target as a north star.
- **Channel Partner Brand Profile content prominence in Home Feed.** Brand Profile content lives in Discovery, not Home Feed primary positions. Surfacing it more aggressively monetizes short-term and degrades trust.

## 17. Risks

- **R1. Empty-room death spiral.** If the Liquidity Unit is not hit in seed metros within ~14 days of cohort onboarding, the social loop collapses and the product reverts to a less-good Planta. Mitigations: Concierge Onboarding of ~100 plant influencers, beachhead-species saturation (Monstera, Calathea), Cadence Injectors as feed support infrastructure, geographic concentration before broadening.
- **R2. In-character posting decays past novelty.** Petzbe's longitudinal engagement past the novelty window is unknown publicly; the same risk applies here. Mitigation: voice tooling investment (custom archetype tuning, per-plant voice continuity), milestone weighting, propagation lineage as a long-arc engagement vector. Validation pass on in-character durability is required pre-scale (OQ-5).
- **R3. Incumbents ship a personality layer within ~90 days.** Greg or Planta could retrofit Voice and Personality Archetype features onto their existing care apps quickly. Mitigation: ship faster; accumulate Plant Profile longitudinal voice data that a retrofit cannot replicate (the §1.3 moat); lock Channel Partner relationships in seed metros.
- **R4. AI assist floods the feed with low-quality output.** FR-12 (no generic AI output ever) is the discipline that prevents this; failure to enforce degrades the §6 Aesthetic and Tone bar and accelerates the Petzbe-month-12 decay risk.
- **R5. Plant influencers have no incentive to fragment Instagram and TikTok reach.** Concierge Onboarding offers per-Plant-Profile analytics Instagram cannot match, but the incentive structure is unproven. Mitigation: per-Plant-Profile analytics depth, exclusive Cohort access, possible revenue share on premium-tier conversions referred.
- **R6. Cringe risk from early base content.** Format depends on early base producing non-cringe content; bad early content spirals via discovery surfacing. Mitigation: Concierge Onboarding sets the early bar, archetype defaults bias outputs, moderation patterns established pre-launch.
- **R7. Trust and safety overhead.** AI voice, marketplace adjacency, and minor-user growth compound moderation cost beyond initial team sizing. Mitigation: pre-publication AI moderation (§13.1), staged feature rollout (no marketplace v1), explicit minor-user policy at launch.
- **R8. Northern-hemisphere seasonality.** Engagement collapses in winter for the bulk of the user base. Mitigation: dormancy-stage Cohorts and seasonal challenges as feed-active content during low organic-event months, premium tier features that hold value year-round (analytics, archive). Open Question OQ-9 asks whether southern-hemisphere or grow-light cohorts need explicit strategies.

## 18. Post-v1 Adjacent Value

Adjacencies surfaced for awareness and roadmap planning. None are v1; all are explicitly out of scope (§14, §15). Their inclusion here is to prevent silent loss; downstream UX and architecture should be aware that these are plausible extensions and avoid foreclosing them.

- **Plant inheritance and legacy.** Plant Profiles transfer on gift, breakup, or death. High emotional weight; organic invite moment. Reinforces the per-plant-identity-with-continuity thesis (§1.2). v2 candidate; depends on owner-transfer feature in §9.1 Notes.
- **Climate cohort advisory.** Hyper-local advisory feeds ("plants in your zip stressed by this week's heat dome"). Recurring press story format; B2B data-product surface for retailers and insurers. Requires aggregate-data opt-in framework (§13.2). v1.5 / v2 candidate.
- **Mental health and wellness framing.** Plant care correlates with documented therapy and recovery outcomes. Wellness-brand partnership opportunity; broader press narrative beyond the plant industry. Voice and brand do not lean into wellness in v1 (§6 anti-references), but the adjacency is acknowledged as long-term value.
- **Care-history-as-provenance.** For rare-plant resale (aroids, variegated cultivars, etc.), a verifiable per-plant Care Event Log functions as eBay-feedback-equivalent buyer signal. Unlocks the marketplace surface that v1 explicitly defers (§14). v2 candidate.
- **Species-cohort SEO.** Every species page can become a public, indexed community hub pulling search traffic from "why is my [species] yellowing" queries. Distribution channel that compounds with content volume. Requires species-page IA decisions in v1.5+.
- **Plant care dataset as integration product.** The longitudinal per-plant data accumulated over years becomes the most accurate real-world plant care dataset. Utility apps (PictureThis, PlantNet, Planta) want to integrate, not compete. Reinforces the §3 three-year vision data-gravity claim.
- **Botanical garden and university extension partnerships.** Authoritative species cohorts, content depth, public credibility. Long-arc partnerships; not v1 priority.
- **Cross-promotion with Petzbe.** Personification adjacency. Pet-and-plant household overlap is plausibly material; cross-promo costs little and reinforces both brands.

## 19. Open Questions

Numbered for downstream tracking. Each becomes a research task, validation gate, or future ticket.

- **OQ-1. Final brand name.** Working title is Plantsona. Distillate alternatives: Sprout, Leafling, Plantonia, Bloomr, Verdant, Florafriends, Petal, Rooted, Botany. Decision before public marketing assets.
- **OQ-2. Pre-MVP validation cohort.** Distillate-suggested 50 plant collectors validating willingness to post in-character and willingness to pay for premium. Required before committing v1 launch capital.
- **OQ-3. Monetization conversion benchmark.** 5-8% premium conversion is asserted from Strava (~12%) and Letterboxd (~7%) comparables but is unvalidated for plant hobby specifically. Needs benchmark study or controlled launch experiment.
- **OQ-4. Cadence reality check.** 4-posts-per-Plant-Profile-per-month target needs stress test against actual Greg and Planta care-log frequency data and against the Petzbe analog.
- **OQ-5. In-character durability validation.** Is in-character posting sustainable past the novelty window? Validation pass needed before scaling beyond seed metros. (See R2.)
- **OQ-6. Voice moat specificity.** Define concretely: archetype library expansion path, fine-tuning corpus on plant-care text, user-tunable voice memory per Plant Profile, species-specific personality priors. Affects v1.5 and v2 scope.
- **OQ-7. Cringe / quality flywheel definition.** Concrete operational definition: seeded creator quality bar, archetype default tuning, moderation patterns, when to intervene.
- **OQ-8. Launch marketing surface for non-wedge features.** v1 wedge is the milestone-driven feed (this PRD's call; closes the parent distillate Open Question). Propagation Lineage and Cohort Discovery are v1 features but secondary in launch narrative. Surface them in v1 marketing, or hold as v1.5 reveals?
- **OQ-9. Seasonal strategy.** Northern-hemisphere winter dormancy is acceptable as engagement trough, or do we need southern-hemisphere recruitment and grow-light Cohort strategies as v1.5?
- **OQ-10. Free-tier Plant Profile cap.** What is the right cap for the free tier (above which premium is required to add more Plant Profiles)? Affects perceived value of premium and free-tier user lock-in.
- **OQ-11. Team size, capital, timeline.** Not specified in distillate; precondition for committing to milestone dates and v1.5 scope. (Belongs in business plan, not PRD; flagged here for completeness.)
- **OQ-12. Final Personality Archetype set.** v1 set is Sassy, Anxious, Regal, Chaotic, Stoic. Need user research validating coverage and naming; pilot in pre-MVP validation cohort (OQ-2).
- **OQ-13. Life Stage taxonomy.** v1 set is establishing, healthy, recovering, blooming, propagating, dormant. Validate against actual care-event signals during pre-MVP testing; refine before scale.
- **OQ-14. Brand Profile premium-tier eligibility.** Whether Channel Partners can subscribe to premium-tier features is deferred to v1.5 Channel Partner spec (§7 Glossary, §11). Decide as part of nursery monetization model.

## 20. Assumptions Index

Inline `[ASSUMPTION]` tags from the document, surfaced for explicit confirmation in this and future sessions.

- **A1.** §6 Personality Archetype list (Sassy, Anxious, Regal, Chaotic, Stoic) is the correct v1 starting palette. (Linked to OQ-12.)
- **A2.** §7 Glossary Life Stage taxonomy (six states: establishing, healthy, recovering, blooming, propagating, dormant) covers v1 cases. (Linked to OQ-13.)
- **A3.** §8 Care Hub as a distinct top-level surface (not nested under Profile) is the correct IA call.
- **A4.** §9.1 FR-6 archive-not-delete is the right v1 stance for preserving Propagation Lineage integrity.
- **A5.** §9.5 FR-27 PDF download is the dominant non-app sharing path for Claim QRs.
- **A6.** §11 Premium pricing band ($4.99-$7.99/month) and 5-8% conversion target derived from Strava and Letterboxd comparables. (Linked to OQ-3.)
- **A7.** §13.1 Terms-of-service age gate (13+) is sufficient minor-user protection at launch.
- **A8.** §13.3 Free-tier rate limiting is sufficient AI inference cost control for v1.
- **A9.** §13.3 Free-tier photo compression is acceptable; full-resolution originals retained for premium tier only.
- **A10.** §15.1 Monstera and Calathea are the correct beachhead species for Cohort seeding.
- **A11.** §16.2 April-October Northern-hemisphere seasonality profile is correct for the bulk of v1 user base; southern-hemisphere recruitment strategy is OQ-9.
