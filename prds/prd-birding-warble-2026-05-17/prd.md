# Warble PRD

**Product:** Warble, a birding companion
**Author:** Product (Birder-PM)
**Date:** 2026-05-17
**Status:** Draft v0.1
**Audience:** Founders, ML lead, design lead, partnership lead (eBird / iNat outreach)
**Working name:** Warble (subject to trademark clearance)
**One-line:** A respectful birding companion that listens, looks, and remembers.

## Vision

The birding community already has its anchors.

Merlin handles quick ID. eBird is the citizen-science backbone. iNaturalist captures the broader natural-history record. The Macaulay Library holds the sound archive most of us learned on.

Warble is not trying to replace any of that.

Warble is a thin, respectful companion that does three things well in one place. It tells you what is singing around you right now. It gently confirms what you are looking at through the lens. And it keeps your life-list in a form that travels with you.

The angle is AR plus on-device speed plus audio-archive ergonomics, with the existing community's data plumbing treated as a first-class citizen, not a wrapper to be siloed. We assume the user already has an eBird account or will get one; we are not the system of record, we are the field interface.

## Target user

Two overlapping segments. The distinction matters because they want different defaults, and getting the defaults wrong loses one or the other.

**Serious birders.**

Keep a life-list. Know their local hotspots. Already have an eBird account, probably a Macaulay upload or two, possibly a Sibley app on their phone alongside ours.

They want speed in the field, an audio archive that survives the trip home, and a life-list that syncs to eBird without friction. They are skeptical of new apps and will leave instantly if Warble feels like a toy. AR is interesting to them only if it is unobtrusive and accurate. They will forgive a missing feature; they will not forgive a confident wrong ID.

**Casual birders.**

Notice birds on hikes, in the backyard, on vacation. May not know a Song Sparrow from a House Sparrow, and that is fine.

They want help identifying what they hear and see, and a place to remember what they have spotted. They are the AR-overlay audience: the soft outline and species name is genuinely magical for someone who has never picked up a field guide. Many will graduate into serious birders if the on-ramp is good, and that path is part of the product.

Warble works for both because the defaults are quiet and the depth is opt-in. A casual user never sees eBird sync settings unless they go looking; a serious user finds them in the first session.

## Capability surface

- **Audio ID.** Real-time soundscape listening, multi-species per clip, confidence scores. Robust to wind, water, and traffic noise. Designed for the "stop on the trail and just listen" moment.
- **Visual ID.** Still photo and live AR view. High-confidence for common species. Deferred (not guessed) for rarities, because a confident wrong ID is worse than an honest abstention.
- **AR overlay.** Soft species name and outline on the live camera view. Opt-in per session. Subtle by design.
- **Life-list.** Lifer entries with date, location, photo, audio clip, and free-text notes.
- **Trip mode.** Start and end a trip. Trip log aggregates IDs, sightings, and notes for the session, and can be promoted to a full eBird checklist on submit.
- **Personal best.** Rarest species seen per region, for the user's own reference. Not leaderboarded, not shared by default.
- **eBird integration.** Sync your life-list. Optionally submit checklists from a finished trip.
- **iNaturalist integration.** Cross-post an observation with one tap.
- **Field-guide entry per species.** Range map, similar-species comparison, multiple vocalization examples (licensed from Macaulay).
- **Offline mode.** Download a region (state-sized) for audio ID, visual ID, and field-guide content without signal. Designed for the rural birder with no bars.

## ML model details

**Audio ID model.**

Bird-vocalization classifier trained on a multi-million-clip dataset assembled from Xeno-Canto, Macaulay Library, and eBird-linked recordings, under appropriate research-use terms. 1000+ species at launch, weighted toward the Americas and Western Palearctic for v1.

Architecture: convolutional front-end on mel-spectrograms, transformer head for multi-species attention within a window. Trained with strong augmentation (wind, water, traffic, overlapping song) so wild-condition accuracy holds up.

Target: top-3 species correct in 92% of clear-vocalization conditions in field testing, not lab benchmark. Latency target: identification within 3 seconds of a clear vocalization on a modern phone.

**Visual ID model.**

Image classifier covering roughly 3000 common species globally. Built on a pretrained vision foundation model with birding-specific fine-tuning on labeled iNaturalist and Macaulay still-image sets.

Designed to abstain on low-confidence inputs rather than guess. A rare-bird false positive is more damaging to user trust than a polite "not sure." This shapes the loss function and the UI both.

**Inference.**

On-device wherever possible. Apple Neural Engine on iOS, NPU on modern Android. Cloud fallback for low-confidence cases and for the long tail of species not packed into the on-device model.

AR overlay uses a lightweight CoreML / ARCore path and is explicitly not always-on. Birding etiquette frowns on flashing screens, and a bird in the open is a moment of presence, not a moment to look at your phone. The user chooses when to turn it on.

**Stretch goal.** Licensed model from a research institution (Cornell Lab or equivalent) rather than fully in-house, if a partnership can be structured. This is a v2 conversation, not v1, and we do not gate launch on it.

## Community integration

- **eBird.** Official partnership is the target. v1 ships with public API integration for list sync and optional checklist submission. Partnership conversation runs in parallel.
- **iNaturalist.** Cross-post observations from Warble to iNat in v1 via their public API. No deeper integration committed.
- **Macaulay Library.** License the reference audio clips that play in the field-guide section under standard terms. Do not redistribute the model training corpus.
- **Audubon, ABA, regional bird clubs.** No technical integration. Pursue discoverability and credibility through partnership conversations, podcast appearances, and being a known good citizen at festivals.

We are not building a competing data layer. Warble's database of record for a serious birder's life-list is eBird, with Warble as the front-end that makes it pleasant to use in the field.

## Privacy and location-data handling

Location data in birding is sensitive in a way most app categories never have to think about. A precise GPS pin on a nesting Great Gray Owl is genuinely dangerous to the bird. We follow the conventions the community already trusts.

- **Rare-species protection.** Following eBird's Sensitive Species model, exact GPS coordinates of rare-species observations are blurred to the nearest county or region by default. The user can opt to share precise coordinates privately with eBird or a trusted contact, but Warble itself does not display or transmit precise rare-species locations to other Warble users.
- **Life-list privacy.** Private by default. Sharing is per-list, opt-in, and revocable. A user can share their full life-list with a single friend, with a club, or publicly, and can unshare at any time.
- **No third-party sale.** Location data is never sold or shared with advertisers, data brokers, or partners. The only outbound location flow is the eBird / iNat sync the user explicitly enables.
- **Audio clips.** Stored locally first. Cloud backup is opt-in and tied to the Pro tier. User-contributed audio is not used to train the model without explicit, per-clip consent and a clear license path. If a user wants to donate a clip to Macaulay, we make that one-tap.
- **Children and schools.** No special program at launch, but the privacy defaults are conservative enough to be classroom-safe out of the box.

## Constraints

- iOS and Android at launch. Not web, not watchOS, not Vision Pro in v1.
- Free tier: full audio ID, full visual ID, basic life-list capped at the most recent 100 lifers.
- Pro tier at $24/year: unlimited life-list, offline regions, AR overlay, eBird sync, audio recording archive, cloud backup.
- Audio ID latency: identification within 3 seconds of a clear vocalization on a modern phone.
- Offline region size: roughly 30MB for a state-sized region, including audio reference clips at compressed quality.
- AR overlay is opt-in per session, not a default mode.

## Non-goals

- Not a competitive leaderboard. Birding culture is rivalry-averse, and the few apps that have tried this have alienated the community. Personal best is for the user's own reference.
- Not a social network. eBird, iNat, and ABA forums already serve this role. We do not need another feed.
- Not selling guided birding tours, optics, or gear.
- Not a backyard-feeder monitoring product. That category (Bird Buddy and similar) is its own thing.
- Not building our own species taxonomy. Defer to Clements taxonomy and the IOC World Bird List, and accept their updates on their schedule.
- Not training the model on user-contributed data without explicit consent and a defensible licensing path.

## Success signal

- **Active life-listers.** Users who log at least 5 new lifers within their first 3 months. This is the closest proxy to "this person is actually using Warble in the field" rather than a curiosity install.
- **Audio ID accuracy in the wild.** Top-3 species correct in 92% of clear-vocalization conditions, measured against a labeled field-test set collected across habitats, not a lab benchmark. Failure modes get logged and reviewed weekly during the first year.
- **Community trust.** Positive reception from the ABA and Audubon communities. Specifically, no "this is a Merlin ripoff" framing in reviews, social, or community forums. We earn this by being clearly differentiated (AR, on-device speed, audio archive) and by being respectful in how we talk about Cornell and Merlin in our own marketing. Trip-report mentions on listservs and club newsletters are a softer but real signal.
- **Pro conversion.** 8% of monthly active users convert to Pro within 12 months. Anchored on offline regions and audio archive as the load-bearing Pro features.
- **Retention through migration season.** Spring and fall migration are the load tests for any birding app. We expect a usage spike in April-May and September-October; retention through the off-season is the harder signal we care about.

## Open questions

- **Cornell Lab partnership.** Do we pursue a licensing relationship for the audio model in v2, or stay fully in-house? The partnership shortens the path to a credible model but creates dependency, and Cornell may reasonably decline.
- **eBird partnership scope.** Public API only (safe, ships v1) versus official partnership (slower, but unlocks deeper sync and joint marketing). Decision affects launch messaging.
- **AR on Android.** ARCore coverage varies by device. Do we ship AR Android-only on a supported subset, or hold AR for an iOS-first launch and bring Android online later?
- **Audio archive ownership.** When a Pro user records a vocalization, do they retain full ownership with us holding a non-exclusive backup license, or do we offer an opt-in donation path to Macaulay? The latter is community-positive but legally heavier.
- **Pricing.** Is $24/year correct, or does the serious-birder segment expect a higher anchor ($36 to $48) given Merlin is free? Test before launch with a small willingness-to-pay study.
- **Regional taxonomy coverage.** Launch with Americas and Western Palearctic only, or stretch to a true global 3000-species visual model on day one? Affects model size, offline region size, and partner conversations.
- **Apple Vision Pro and smart glasses.** Out of scope for v1, but the AR overlay design choices today will either set us up for a clean wearable port later, or paint us into a corner. Worth a design review.
