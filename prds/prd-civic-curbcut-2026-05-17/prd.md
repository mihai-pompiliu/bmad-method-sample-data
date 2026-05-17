# CurbCut PRD

## Document Control

- Product: CurbCut
- Owner: CurbCut Project, a fiscally sponsored 501(c)(3) initiative
- Document date: 2026-05-17
- Status: Draft for advisory board review
- Funding source: 3-year operating grant ($1.2M) from a major civic technology foundation, supplemented by anticipated municipal data-services contracts beginning in year two
- Governance: Board of Directors (majority disabled community members), Disability Community Advisory Board (DCAB) with documented veto authority on product direction, accessibility standards, and partner agreements
- Distribution: DCAB members, partner Centers for Independent Living (CILs), municipal partner liaisons, grant program officer

## Mission

CurbCut exists so that disabled people can navigate cities with accurate information about the built environment. The public right-of-way is a public good. Information about whether that right-of-way is usable is also a public good. We build, maintain, and openly share that information in partnership with the communities most affected by its absence.

We are not a mapping company. We are a civic infrastructure project. Our product is accessibility data and the tools that make it useful.

The grant that funds our first three years was awarded with explicit expectations: that the work would be governed by disabled people, that the data produced would re-enter the commons, and that the organization would build toward operational sustainability without compromising either of those commitments. This PRD is written under those expectations and should be read as accountable to them.

## Target Users

Primary users at launch:

- Wheelchair users (manual and power) navigating urban environments for work, healthcare, errands, and social life
- Mobility device users more broadly, including walker, cane, and scooter users

Phase two users (planned within the grant period):

- Blind and low-vision users, with audio-first routing and integration of tactile paving data where available
- Parents and caregivers using strollers, who share many of the same infrastructure dependencies

Trusted partners and contributors:

- Staff and members of local Centers for Independent Living in launch cities
- Members of the DCAB, who are users first and advisors second
- Volunteer community mappers, many of whom are themselves disabled

Language note: This document uses identity-first language ("disabled people") as the default, consistent with guidance from our DCAB and partner CILs. We do not use "differently-abled" or "special needs." Where individual users prefer person-first language, the app surfaces that preference in account settings. Community-preferred terms (Deaf, blind, low-vision, wheelchair user, autistic) are used as community members use them, with capitalization conventions reviewed by DCAB.

## Problem

Existing consumer mapping products treat accessibility data as a thin overlay on top of car-centric and walking-centric routing. The result is a set of recurring, documented failures:

- Curb cut data is missing, stale, or marked present when the cut is unusable (cracked, blocked, ends in a lip)
- Transit station "accessible entrance" labels do not reflect elevator outages, which can last weeks
- Building entrance information often describes the main entrance only, ignoring step-free side entrances that are the actual accessible route
- Sidewalk surface conditions (heaves, gravel, missing segments) are not captured at all
- Temporary obstacles (construction, snow piles, restaurant sidewalk seating, scooter clutter) are invisible to current tools
- Public restroom accessibility data, where it exists, conflates "ADA compliant on paper" with "actually usable"

The downstream harm is concrete. Disabled people plan longer trips, abandon trips, get stranded mid-route, or are injured attempting routes that current tools described as accessible. The data gap is itself a barrier.

The problem is not a lack of regulation. ADA Title II requires that public entities make pedestrian rights-of-way accessible, and most launch-city governments have transition plans on file. The problem is that the data describing what is and is not accessible, on the ground, today, is fragmentary, stale, and not in the hands of the people who depend on it. CurbCut closes that loop.

## Capability Surface

Functional requirements for v1:

1. Accessibility-aware route planning between two points, with options to avoid stairs, prefer curb-cut-verified crossings, set a maximum gradient, and require step-free building entry at destination
2. Report contribution flow: select category (curb cut, bathroom, transit entrance, sidewalk segment, building entrance, temporary obstacle), drop or confirm a location, add optional photo, set a severity ("blocks me," "hard," "OK"), and add optional free-text context
3. Verification flow: nearby users see recent reports and can confirm, dispute, or update them; reports with three or more independent confirmations within seven days surface as high-confidence to all users
4. Saved places: users can save frequently visited locations with personal accessibility notes (these notes are private by default and never shared without explicit per-place opt-in)
5. Offline mode: users can download a defined area for offline routing and report viewing; queued reports sync when connectivity returns
6. Transit integration: pull live elevator and escalator outage feeds from partner transit agencies where available; clearly label data freshness
7. Screen reader support: the app is fully usable with VoiceOver and TalkBack, validated by paid blind and low-vision testers, not solely by automated tooling
8. Language support: English and Spanish at launch, with ASL-glossed video help content for core navigation tasks
9. Account and consent surface: minimal required account data, granular consent for any optional contribution attribution, clear export and delete

Accessibility is not a section in this document. It is the product. Every requirement above is judged against whether it works for the users described, validated by those users, before release. WCAG 2.2 AA is the floor for the app and the website, not the ceiling, and conformance audits do not substitute for testing with disabled users.

## Data Sources

CurbCut combines four data streams:

- OpenStreetMap, including existing accessibility tags (kerb, wheelchair, tactile_paving, incline) and the broader pedestrian network
- Municipal open data portals in launch cities (curb ramp inventories, sidewalk condition surveys, ADA transition plan datasets, public restroom inventories)
- Partner transit agency feeds for real-time elevator and escalator status where published
- Community-contributed reports and verifications, gathered through the app

Cleaned accessibility data flows back to the commons. Curb cut, sidewalk, and entrance data with sufficient confidence is contributed to OpenStreetMap under ODbL. Aggregated and de-identified datasets are offered to municipal partners under data-sharing agreements and to academic researchers under appropriate licenses (typically CC BY-SA for derived datasets, with case-by-case review). Users are informed, in plain language, that their verified reports help build this commons.

Launch cities: Chicago, Boston, Portland (OR). Selection criteria were the maturity of municipal open data, the strength of existing relationships with local CILs, and DCAB recommendations. Each launch city has a named municipal liaison and a named partner CIL. Expansion to a new city requires both before any data collection begins.

## Privacy Posture

Disabled users face documented harms from location tracking and from disclosure of disability status. CurbCut treats both as sensitive by default.

- We do not require, request, or store disability identifiers. Routing preferences are stored as preferences (avoid stairs, max gradient), not as health or disability data.
- Location data is used to provide service and is retained only as long as needed. Background location is opt-in, off by default, and explained in plain language at the moment it is requested.
- Reports are attributed only if the user opts in to attribution per-report; default is anonymous.
- We do not sell data. We do not run ads. We do not share individual-level location or contribution histories with municipal partners, researchers, or anyone else.
- Data shared with partners and the commons is aggregated, de-identified, and reviewed by the DCAB before any new sharing agreement is signed.
- Account deletion removes all attributable contribution history within 30 days, while preserving the de-identified facts about the built environment that those reports established.

## Constraints

- WCAG 2.2 AA is the minimum standard for the app and the website. Failures are launch blockers.
- Pre-release testing by paid disabled community testers, recruited through partner CILs, is required before every major release. Compliance audits are a complement, not a replacement.
- Available in English and Spanish at launch. ASL-glossed video help covers core navigation tasks.
- Free to users. Ad-free. No data sale. Funded by grants and by government and research contracts for cleaned, aggregated data products.
- iOS and Android both required at launch. Android-first parity is non-negotiable; data from partner CILs indicates a disproportionate share of users are on Android, often on older devices.
- Offline-first design throughout. Many users have constrained data plans or operate in transit environments with unreliable connectivity.
- All partner agreements with municipalities, transit agencies, and researchers are reviewed by the DCAB before signing.

## Non-Goals

- Not a general-purpose mapping app. CurbCut lives next to Google Maps and Apple Maps and differentiates on accessibility data quality and trust.
- Not a paratransit booking or ride-share app.
- Not a marketplace. No commercial transactions, no business listings sold, no sponsored placements.
- Not a demographic surveillance tool. We do not collect demographic data on users beyond optional, aggregated, clearly-consented research participation.
- Not an autonomous-vehicle accessibility integration in v1. The space is too speculative and the user need is not validated.
- Not built on city-by-city manual data entry. Scalability depends on open data, OSM, and community contribution.

## Success Signal

We will know CurbCut is working when:

- Coverage: 80% of curb cuts in launch cities are mapped (present, absent, or unusable) within 12 months of launch in that city
- Verification quality: the median report receives three or more independent community verifications within seven days
- Retention: 40% of users who complete onboarding return at least once per month after their first month
- Community trust: the DCAB affirms product direction in semi-annual reviews and renews that affirmation in year two and year three
- Data return: at least one official municipal data product (a transition plan update, an open-data refresh, or a capital planning input) references CurbCut data within 18 months of launch

We will not treat downloads, sign-ups, or session counts as primary success measures.

## Risks and Mitigations

A short list of risks the team and DCAB have flagged for active monitoring:

- Data accuracy risk: a wrong "accessible" label is worse than no label at all, because it can route a user into a stranded situation. Mitigation: severity-weighted confidence model, conservative defaults, prominent freshness indicators, easy "this is wrong" reporting from inside a route.
- Surveillance risk: an app used primarily by disabled people that collects location is a sensitive dataset. Mitigation: minimal collection, no demographic linkage, default-anonymous contribution, regular third-party privacy review commissioned by the board.
- Capture risk: well-intentioned municipal or vendor partners may push for product changes that prioritize their reporting needs over user needs. Mitigation: DCAB veto authority, public documentation of partner asks and product responses.
- Sustainability risk: grant funding ends in year three. Mitigation: a planned mix of municipal data-services contracts, research partnerships, and a small recurring-gift program. Any sustainability path that requires advertising, data sale, or user fees is excluded by policy.
- Burnout risk: the disability community is repeatedly asked to do unpaid labor for civic-tech projects. Mitigation: paid tester program, paid DCAB stipends, paid advisory engagements for community contributors who provide expertise beyond standard contribution flows.

## Open Questions

- What is the right confidence threshold for a community report to surface as high-confidence, and should it vary by category (a curb cut is more stable than a snow obstacle)?
- How should we handle conflicts between municipal open data and community reports, when the community reports are likely more current but the municipal data is the legal record?
- What is the operational model for expanding beyond launch cities? Self-serve community onboarding, partner-led expansion, or a hybrid with explicit DCAB review per city?
- For phase two (blind and low-vision users), what is the right primary input modality, and how do we avoid building two parallel apps under one name?
- What is the long-term funding mix once the founding grant ends, and which of the contemplated revenue paths (municipal data contracts, research partnerships, public-sector services) is most aligned with the mission?
- How do we recognize and compensate community contributors in a way that respects mutual aid norms in the disability community and avoids extractive "gamification"?
- What is our position on integration requests from third-party apps (transit apps, university campus apps, hospital wayfinding) that want to embed CurbCut data, and what license terms protect users and the commons in those arrangements?
- How do we handle a launch-city partner that materially fails to maintain its open data, given that we have committed publicly to using that data and our users depend on it?

## Appendix: Definitions

A short shared vocabulary used in this document:

- Curb cut: a sloped section of sidewalk that meets the street, allowing wheeled access between the sidewalk and the roadway.
- CIL: Center for Independent Living. A community-based, disability-led organization providing peer support, advocacy, and services. CILs are our primary community partners.
- DCAB: Disability Community Advisory Board. The body with veto authority over product direction in this project.
- Commons: the shared body of openly licensed accessibility data, including OSM and municipal open data portals, to which CurbCut contributes.
- High-confidence report: a report with three or more independent community verifications within the configured verification window.
