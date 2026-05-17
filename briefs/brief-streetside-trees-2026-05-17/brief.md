---
title: StreetSide Trees
status: idea-stage
created: 2026-05-17
---

# StreetSide Trees

*Know every tree on your block. Adopt one. Help your city see its canopy.*

## Problem

Cities have terrible street tree inventories.

The data, where it exists at all, is years out of date, locked in municipal GIS systems few residents can read, and almost never reflects what is actually growing on the block right now. A storm takes out a maple, a developer rips up a row of pears, a new sapling gets planted by a neighbor with a shovel and good intentions, and none of it shows up anywhere a human can find.

Meanwhile, the people who care most about urban trees (hobbyist arborists, climate-curious residents, neighborhood watchers, the retired guy who has been walking the same loop for twenty years) have no place to put what they know.

The canopy gets less green every summer, the heat islands get hotter, and the institutional knowledge of which trees are thriving and which are dying lives in a few people's heads, then disappears when those people move or pass on.

## Target user

The primary user is a curious resident in their thirties to seventies who already notices trees. They take walks. They have opinions about the sycamore on the corner. They might own a houseplant book, follow a few naturalist accounts, and have once googled "what is wrong with this leaf." They are not a credentialed arborist, but they are a careful observer, and they want their attention to count for something. They are climate-aware without being climate-paralyzed: doing one small thing on their block feels right-sized.

This is not for professional foresters who already have ArcGIS and a clipboard. It is not for users who want a gamified Pokemon-style species collector. The seriousness of the canopy is part of the appeal.

## Vision

StreetSide Trees is a mobile-first app that turns a neighborhood walk into a contribution.

You open the app, point your camera at a tree, and the app helps you identify it: species, rough age class, visible condition. You drop a pin. Over weeks, your block fills in. Over months, your neighborhood does. Over a year, a small army of walkers has mapped a city. The map is beautiful, public, and yours.

The second half of the product is adoption. Any logged tree can be adopted by a nearby resident. Adoption is light: you get notifications during droughts ("the silver maple at 412 Elm has not had rain in 18 days"), you can log waterings, you can flag damage or disease for a local arborist network to review. It is not a chore app. It is a relationship with one specific living thing on your street.

The third piece is the data layer. The base map is open. Anyone can see and download it. Cities and arborist groups can subscribe to a premium tier that gives them clean exports, change-detection alerts, and a portal to push their own official records back into the map. The goal is a canopy data commons that residents trust because they built it, and that institutions trust because the methodology is transparent.

We are not building a science instrument. We are building a habit, with a serious data layer underneath.

## Key capabilities

- Camera-assisted species ID for street trees, with confidence scores and a path to ask a human when the model is unsure.
- Per-tree records with location, species, estimated age, condition notes, and a photo history over time.
- Tree adoption with lightweight stewardship prompts (drought alerts, seasonal check-ins, damage reporting).
- Neighborhood map view showing logged trees, gaps, and recent activity.
- Open data export at the individual and neighborhood level, free for residents and researchers.
- Municipal and arborist premium tier with change-detection, bulk export, and a write-back portal.
- Lightweight social layer: see who else walks your blocks, hand off adoptions when someone moves.
- Offline logging for walks through low-signal areas, with sync when back online.

## Why now

Phone cameras and on-device vision models are finally good enough to do plausible species ID without sending every photo to a server.

Climate anxiety is translating into local, tangible action in a way it was not five years ago. Cities under heat-island pressure are actively shopping for canopy data, and most of them cannot afford a real inventory. State and federal grants for urban forestry have grown meaningfully in the last two years. The supply of motivated walkers and the demand from municipalities are both real, at the same time, for the first time.

## Constraints and assumptions

- Identification accuracy will not be perfect, and the product has to be honest about that without becoming useless.
- Privacy matters: tree locations are public, but walker patterns are not, and the product cannot leak where individuals walk.
- Municipal sales cycles are slow; the consumer side has to stand on its own for at least the first two years.
- Adoption only works if it stays light. The moment it feels like homework, retention collapses.
- We assume a meaningful fraction of users will contribute without payment, the way iNaturalist and eBird users do.
- We assume at least one mid-sized city will pilot the premium tier within twelve months of a credible map.

## Success signals

- A single neighborhood reaches 70 percent tree coverage in the map within six months of launch there.
- Adopted trees show measurably better survival rates during drought events than unadopted ones nearby.
- At least one city signs a paid pilot citing the resident-collected data as the reason.
- Users describe the app, unprompted, as something they look forward to opening on a walk.
- A meaningful share of logged trees get adopted within their first month on the map.

## Open questions

- Is "adoption" the right frame, or does it imply more responsibility than people want to take on?
- How do we handle disputes when two residents both want to adopt the same tree?
- What is the right relationship with iNaturalist, which already does species ID well but is not street-tree focused?
- Do we need a desktop experience at all, or is mobile sufficient for everything except the municipal portal?
- How do we keep the data trustworthy enough for cities without making contribution feel like a science project?
- Should the open data license be CC0, CC-BY, or something stricter to prevent municipal vendors from reselling resident contributions?
- What is the right first city? Somewhere with strong civic tech culture, or somewhere with desperate canopy loss and no existing inventory?
