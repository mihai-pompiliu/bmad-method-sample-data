# PRFAQ: Rendezvous

## Press Release

**FOR IMMEDIATE RELEASE**
**May 17, 2026**

### Rendezvous Launches the Easiest Way to Plan a Weekend with a Faraway Friend

AUSTIN, TX. Today Rendezvous, a new consumer mobile app for iOS and Android, opened to the public in the United States. Rendezvous solves a problem that quietly frustrates millions of adults every year: two friends who live in different cities want to see each other for a long weekend, but planning the trip collapses under the weight of flight searches, weather guesses, vibe negotiations, and the dreaded group text that loses momentum after the third message. Rendezvous turns a week of back-and-forth into a single five-minute session on a phone.

The app works in a single flow. One friend invites the other by phone number or link. Each person spends about a minute answering what they like to do on a trip (outdoors, food, nightlife, museums, slow mornings), the weekend windows that work for them, and the airports they would fly from.

Rendezvous then searches live flight prices from both origins, filters to destinations that respect a shared "no more than four hours in the air" rule, scores each candidate city against both friends' vibe profiles, layers in the seasonal weather forecast, and returns a ranked short list of three to five long-weekend trips. Each option shows total cost per person, flight times, a daily weather snapshot, and three concrete things to do that both friends will actually enjoy.

"Most travel apps assume you already know where you want to go. The hard part for adult friendships is not booking, it is agreeing," said Priya Natarajan, co-founder and CEO of Rendezvous. "We built Rendezvous because my college roommate lives in Seattle, I live in Atlanta, and we spent two years failing to pick a city. The app picks the city in five minutes and gets out of the way."

In a closed beta that ran from January through April 2026, more than 4,200 pairs of friends used Rendezvous to plan trips. Eighty one percent of pairs who started the flow agreed on a destination in their first session, and the median time from invite sent to destination locked was eleven minutes. Beta user Marcus Chen, a software engineer in Chicago, used Rendezvous to plan a March trip with a college friend in Denver. "We had been trying to schedule this since 2024. Rendezvous suggested Salt Lake City, which neither of us would have picked on our own. Flights were under three hours for both of us, the weather window was perfect, and it found us a food scene plus skiing in one weekend. We booked it the same night."

Rendezvous is free to download and free to use for unlimited trip searches. The app earns revenue through optional affiliate links when users book flights or hotels through partner providers. The company is backed by seed investors who have backed consumer travel and social products, and it is hiring across engineering and design. Rendezvous is available today on the App Store and Google Play in the United States, with international flight data planned for 2027.

---

## Frequently Asked Questions

### Customer-facing

**Q: Is Rendezvous free?**

A: Yes. The app is free to download and free to use. There is no paywall, no trip limit, and no premium tier at launch. We earn revenue only if you choose to book through one of our partner links, and the price you see is the same price you would get going direct.

**Q: Does Rendezvous book flights and hotels for me?**

A: Not directly. Once you and your friend pick a destination, Rendezvous hands you off to flight and hotel booking partners (currently Kayak, Expedia, and Booking.com) where you complete the purchase.

We considered building booking in-house, but the support burden of handling cancellations, refunds, and schedule changes would have crushed a small team. Handoff lets us stay focused on the picking problem, which is the part nobody else does well.

**Q: What if there are three or more of us?**

A: At launch, Rendezvous supports pairs only. Group dynamics get exponentially harder past two people, and the matching math changes meaningfully. We are running a small pilot with groups of three later in 2026. Groups of four or more are on the roadmap but not committed.

**Q: What if my friend does not want to download an app?**

A: They do not have to. You can share a web link that lets them complete their preference profile in a mobile browser in about a minute. They will get the final destination short list by text. They never have to create an account, though they can if they want to plan future trips.

**Q: How accurate are the flight prices?**

A: Prices update every fifteen minutes from our flight data partner. They are accurate within a few dollars at the moment you view them, but flight prices change constantly, so the price you book at may differ slightly. We always link out to the live booking page so you see the real number before you pay.

**Q: What about driving instead of flying?**

A: Not at launch. The four-hour-flight rule was a deliberate constraint to keep the product focused on the specific pain of long-distance friend trips, which is where existing tools fail hardest. A road-trip mode is a plausible future product but it is a different scoring problem and we are not building it yet.

**Q: Does it work for international travel?**

A: Not at launch. United States domestic flights only. International flight data, currency handling, and visa awareness add complexity we want to get right rather than ship rough.

**Q: What if the suggested destination has no good weather for our dates?**

A: The app will say so. If none of the in-range destinations have a decent weather forecast for your weekend, Rendezvous shows that result honestly and suggests either shifting the weekend by one or two weeks or relaxing the four-hour-flight rule. We would rather show you bad news than a fake recommendation.

### Stakeholder and internal

**Q: Why this market, why now?**

A: Three forces converged. First, remote work scattered close friends across cities at a rate no prior generation experienced, and the Surgeon General's 2023 advisory on loneliness made the social cost of that scattering legible.

Second, flight data APIs (Duffel, Kiwi, Amadeus self-serve) finally became affordable for a small team. Third, large language models made vibe-matching from short free-text answers tractable without a research team.

None of those three were true in 2020.

**Q: How does the matching algorithm work without being creepy?**

A: We score destinations, not people. Each user fills out a short preference profile (six to eight questions) that produces a vector across eight vibe dimensions: outdoors, food, nightlife, arts, history, wellness, shopping, slow. Each candidate destination has its own vibe vector, curated and updated quarterly.

The match score is the cosine similarity between the destination and the midpoint of the two users' vectors, with a small penalty for destinations that are weak in any dimension either user rated highly.

We do not analyze messages, social graphs, photos, or location history. We do not infer demographics. The only inputs are the answers each user explicitly typed in.

**Q: What is the moat?**

A: Three things compound. First, the destination vibe database is hand-curated and improves with usage feedback, which is hard to clone in a weekend. Second, the matching model is tuned on beta data showing which suggestions pairs actually booked versus dismissed, and that data set grows with every trip.

Third, and most important, the brand association with "the app that fixes the friend-trip group text" is the kind of single-job positioning that is expensive to displace once it lands. None of these alone are a moat. Together they buy us roughly an eighteen-month head start.

**Q: What is the monetization path?**

A: Phase one is affiliate revenue on flight and hotel bookings, which we expect to cover infrastructure and a small team at roughly 50,000 monthly active pairs.

Phase two, planned for late 2027, adds a low-cost subscription ($3 to $5 per month) for power features: unlimited stored trip history, weather and price alerts on saved destinations, and group support.

Phase three, only if the first two work, is curated experience partnerships (a specific restaurant, hike, or class) with revenue share. We are deliberately not pursuing advertising. Ads inside a planning flow degrade the only thing the product is good at.

**Q: Why launch with United States flight data only?**

A: Three reasons. Cost: international flight data is roughly four times the per-query price of domestic. Complexity: international trips add passport, visa, and currency variables we are not ready to handle well.

Focus: the long-distance friend problem is acute inside the US because of remote work scattering, and the US market alone is large enough to validate the product. We expect to add Canada in Q1 2027 and Western Europe in Q3 2027.

**Q: What is the privacy story for the friend who is invited but did not sign up?**

A: The invited friend can complete their preference profile in a browser without creating an account. We store their answers under an opaque invitation token, not a profile. If they never sign up, we delete the token and their answers thirty days after the trip date.

We do not send them marketing email. We do not share their answers with the inviting friend in raw form, only as part of the aggregated destination ranking. This is documented in plain English in our privacy policy, not buried.

**Q: What are the biggest risks?**

A: Three. First, flight-data cost per query could rise and squeeze unit economics before we reach scale. Second, a large incumbent (Google Travel, Airbnb, Expedia) could ship a clone in a quarter. Third, the underlying behavior (adults planning friend trips) may be smaller than our beta suggests because beta users self-selected for high intent.

We are mitigating cost by caching aggressively and pre-computing common origin-pair queries. We are mitigating incumbent risk through brand speed and the single-job focus. We are mitigating demand risk by watching weekly active pair retention closely.

**Q: What does success look like at twelve months?**

A: 200,000 monthly active pairs, a seventy percent rate of pairs completing the full flow once started, a thirty percent rate of pairs returning within nine months to plan a second trip, and affiliate revenue covering operating costs. We will publicly share these numbers each quarter.

**Q: How is this different from Hopper, Kayak Explore, or Google Flights Explore?**

A: Those tools answer "where can I go from my airport for this price." Rendezvous answers "where should the two of us go together." The unit of input is a pair, not a person, and the output respects both people's flight time, budget, and taste at the same time. None of the incumbents are organized around two-origin search, because their business model is one traveler buying one ticket. Ours is two friends agreeing.

**Q: Why does the team think they can build this?**

A: The founding team is three people: a former Hopper product lead who shipped that company's first flexible-date search, a machine learning engineer from a recommendations team at a major streaming service, and a designer who shipped two consumer social apps with more than a million users each. We have built flight search, recommendation systems, and consumer onboarding flows separately. Rendezvous is the first time we have put all three together.

**Q: What happens if a destination on the short list has a safety, weather, or political event?**

A: The destination database flags advisories from the State Department, NOAA severe-weather alerts, and major event disruptions. Flagged destinations are suppressed from the short list and the user is told why in plain language. We err on the side of suppressing, not warning and showing.

**Q: Is there an accessibility story?**

A: Yes. The app meets WCAG 2.2 AA at launch. Beyond that, the preference profile includes mobility and dietary questions (optional) that downrank destinations with poor accessibility infrastructure or limited dietary options for users who indicated those needs. This was the single most requested feature from beta and we are glad we got it in for launch.
