---
title: PhotoSpot Caching — Brainstorm Intent
source: brainstorm-photospot-caching-2026-05-31/.memlog.md
date: 2026-05-31
status: ready-for-downstream (bmad-spec / bmad-product-brief / bmad-prd)
---

# PhotoSpot Caching — Brainstorm Intent

## Concept

A geocaching successor where players photograph a location at a specific angle/composition and others must find and re-photograph the exact same viewpoint — given only a general area plus creator clues and hints, never exact coordinates. The core loop is **SEE → SEEK → ALIGN → MATCH**. The defining verb is **ALIGN**, not "locate": you win by matching a viewpoint, not by reaching a coordinate.

Considered as a real buildable app (Next.js + Supabase + Vercel) and as the demo centerpiece for a father-and-daughter YouTube series showcasing the BMad Method.

## The Irreducible Magic / Core Mechanic

A **live ghost-overlay alignment viewfinder + a 0-100% match score**. The hunter's live camera shows a ~30% translucent ghost of the target photo; you physically move until the ghost aligns with reality, and an image-embedding similarity call (e.g. CLIP) returns a real-time match score. Gentle "hotter/colder" feedback as you move.

This single mechanic is simultaneously:
- **the MVP** (the one feature that, if it feels good, makes the game work),
- **the demo money-shot** for the YouTube series, and
- **the viral artifact** — every find auto-generates a shareable "alignment reveal" GIF (your photo morphing into the target).

Build this first. Everything else is amplification.

## Why It Works (key insights)

- **The constraint IS the moat.** Withholding GPS precision is what makes the game both *fun* (human vision + clues must do the navigating) and *cheat-proof* (you must physically reproduce a perceptual viewpoint). The apparent weakness vs. Pokémon Go is the entire competitive advantage — fun and fairness come from the same design choice.
- **Content perpetual-motion machine.** Players generate infinite levels (UGC); the operator only curates and AI-seeds empty regions. Supply scales with the player base (Waze/Wikipedia dynamics). Zero content cost to operate.
- **Compounding viewpoint-dataset moat.** The accumulated human-curated, georeferenced viewpoint dataset becomes uniquely valuable over time (AR, mapping, tourism, ML) — a defensible asset that grows with usage.

## Anti-Cheat & Safety by Design

- **Liveness:** finds must be captured via live camera with gyro orientation + rough GPS + timestamp — defeats screenshot cheating.
- **Orientation match required:** compass/gyro heading must match the target's heading; you can't just hold up a matching image.
- **Perceptual reproduction:** the match itself requires being physically present at the viewpoint — anti-cheat is baked into the core mechanic, not bolted on.
- **Safety guardrails:** creators attest public/legal access; auto-reject spots on private parcels or near rail/water/cliff hazards; no spots near schools or homes; no spot-tied private messaging; community flagging + rapid takedown; a "safe approach" / no-trespass pledge gate before a spot is revealed.

## Emotional Hook & Series Fit

- **Memory / legacy caches:** photograph a place meaningful to you (where you proposed, where grandpa's shop stood) and leave it as a time-capsule for others to find.
- **Generational hand-down (the series spine):** a dad creates a spot for his daughter to find — geo-anchored letters to the future. The demo app and the emotional story are the same thing. This is the narrative backbone of the father-daughter YouTube series.
- **Family expeditions:** a shared family board of spots found together (potential shared "family account" model with the series' companion Fitness app).
- **Brand soul:** the real product is *renewed attention* — re-enchanting the ordinary world, an antidote to doom-scrolling. Tagline: **"Stop scrolling. Start spotting."** A "slow tech" / get-outside-and-look-up positioning.

## MVP Scope (buildable on the series stack)

Next.js + Supabase + Vercel:

- **Supabase `Spots` table** — anchor photo in storage, lat/lng, radius, text clues. (The atomic content unit is a *Spot*: `{anchor photo, general-area polygon, creator clues, difficulty, hints[]}`.)
- **Next.js map** — Leaflet or Mapbox, showing the general-area radius (no precise pin).
- **Camera capture** — hunter shoots the live viewpoint.
- **One embedding-similarity call** — upload the photo, server compares CLIP embeddings against the target, returns a 0-100 score. One API call = the wow.

**Fake/cut for v0:**
- Skip true on-device real-time matching → do a single server-side similarity call per submitted capture (post-hoc score) before investing in a live in-viewfinder overlay.
- Skip gyro/GPS liveness enforcement, multiplayer, AR, and AI seeding for the very first demo; add liveness/safety before any public release.
- Tutorial spot = "photograph your own front door from the angle shown" (zero travel, guaranteed first-find dopamine).

## Modes & Feature Backlog (terse)

**Modes / moods**
- Race vs. Zen (timer vs. serendipitous wander)
- Blind Mode (clues + visual match only, no map dot — pure detective)
- GeoGuessr / Couch Mode (guess where a photo is from home, then graduate to real hunts)
- Bounty Mode (seekers request a view; hiders fulfill it)
- Live duets (two players race to align the same spot in real time)

**Difficulty & spot design**
- Tiers: Plaza (easy/wide) → Alley (medium) → Needle (brutal: a crack in one brick)
- Creator-set area-radius dial (generous 500m vs. "somewhere in this district")
- Mirror spots (stand at the reflection, not the subject)
- Forced-perspective spots (crouch/climb to recreate an illusion)
- Opt-in, clearly-labeled "Impossible" troll spots

**Engagement loops**
- Daily spot (Wordle pattern): one shared daily spot, share your score, build streaks
- Golden-hour nudge ("perfect light in 20 min near you")
- Letterboxing-style passport: each spot has a collectible stamp/sigil
- Variable-ratio "jackpot" rare spots; ethical near-miss feedback at ~85%
- Flow-state matchmaking — serve spots at the edge of player skill

**Content & narrative**
- Narrative Trails (chained spots reveal story + next clue)
- Then-and-Now / historical caches (find the vantage of a 1920 photo; blend old+new)
- Personal Time Machine (revisit and re-shoot your own old spots over years)
- Curated "exhibitions" by guest curators (e.g. "Hidden Reflections of Chicago")
- Cover-songs / homage mode (re-create a famous photographer's iconic shot)

**Sensory / format variants**
- Motion Caches (reproduce a 3-second camera pan, not a frame)
- Audio Caches (hunt by recreating an ambient soundscape)
- AR rewards (nailing alignment spawns an AR object visible only from that viewpoint)

**Skill-building**
- Photo-quality bonus (better-composed re-creations score higher — teaches photography)
- AI riddle-helper for writing poetic clues
- AI: auto-generate easy/medium/cryptic clue variants; auto-rate difficulty
- AI "describe this view" narration for low-vision accessibility

**Community**
- Spot Clubs, neighborhood leaderboards, club-vs-club seasons
- Creator reputation / "follow this curator for new drops"
- Find-history guestbook + first-finder provenance badge ("first human to align this view")
- Swarm-sourced directional breadcrumb hints from prior finders

## Business Model

- **Freemium:** free hunting; pro tier for advanced stats, unlimited **hint reveals** (pay-to-skip-frustration, never pay-to-win), and offline city packs. Cosmetic map skins + creator tip jar.
- **Sponsored reward spots:** clearly-labeled, reward endpoint is a local cafe ("show this find for a free coffee") — drives real foot traffic, revenue share with merchants.
- **B2B data licensing:** anonymized "best viewpoints" dataset licensed to map / tourism / AR companies.

## Open Questions / Risks

- **Matching accuracy & thresholds:** what similarity score reliably means "aligned"? Tuning per scene type; false positives/negatives erode trust.
- **Abuse & safety moderation at scale:** stalking/luring misuse, dangerous spots, takedown latency — needs real moderation tooling, not just flags.
- **Cold-start per city:** every new region launches empty; AI seeding from Street View/satellite must be good enough to feel alive (and legally clean re: imagery sources).
- **Battery / data drain:** constant camera + GPS is expensive; need on-device lightweight matching and a low-power "cold trail" mode that wakes the camera only in range.
- **Liveness vs. UX friction:** gyro/GPS/timestamp checks must stop cheating without making honest finds annoying.

## Naming Candidates

Parallax (literally about viewpoint shift), Vantage, EarthLens, Spotted, Re:View, Frame Hunt, Same Spot. Mascot: a friendly wandering "Scout" (kid-friendly for the family demo).
