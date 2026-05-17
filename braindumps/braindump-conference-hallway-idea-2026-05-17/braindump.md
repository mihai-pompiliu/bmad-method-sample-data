ok ok ok

just walked out of the priya nair onboarding talk at saastr and i need to get this down before i lose it. line at blue bottle is insane btw. iced oat latte $8.50 lol.

the whole thesis was: onboarding is not a phase, its a product. like a real product with a PM and a roadmap and metrics. and im sitting there going. wait. we dont have that. we have a "customer success" doc in notion that hasnt been touched since the H2 reorg.

THE IDEA
====
what if we built a "first contract in 7 days" guided path inside Clausemate. like literally a checklist that lives in the app, not a separate playbook pdf that nobody opens. you sign in for the first time and theres a side panel that says: 1) upload your standard MSA template, 2) tag your top 3 clause risks, 3) invite legal, 4) run your first redline. and it doesnt go away until you do it.

and the kicker. priya said the magic number isnt time to value its time to SECOND value. the moment they do the thing twice. because the first time could be a fluke or a forced demo. second time is muscle memory.

we are SO bad at second value. we celebrate the first signed contract in slack and then radio silence. customers go dark for 3 weeks and then we wonder why they churn at renewal.

ADJACENT THING 1
====
she mentioned this concept of "onboarding telemetry" where you instrument every step like its a funnel. we have product analytics on the app but NOTHING on the onboarding flow itself. we literally cant answer "where do legal ops teams get stuck on day 4" without asking the CSM to go look. thats nuts.

could we just... pipe the milestone events into amplitude? i think marcus already has the event spec half done from the redline-v2 work. wait actually he might have ripped it out. need to check.

ADJACENT THING 2
====
ok this one is weirder but stay with me. she had a slide about onboarding as a wedge for expansion. like the same flow that gets you to first value also surfaces the next module. so in our case, someone finishes their first redline and the system goes "hey looks like you tagged 4 indemnity clauses, want to try our obligations tracker?" instead of waiting for the AE to notice in QBR.

we have been ARGUING about whether obligations tracker is a standalone product or an upsell for like 6 months. maybe the answer is its an onboarding milestone. like its not a product its a graduation.

god i need to write that down bigger. ONBOARDING AS THE EXPANSION ENGINE. thats the deck title.

THE THING THIS CONNECTS TO
====
ok so remember the whole debate in the april roadmap meeting about "why are enterprise deals taking 11 weeks to go live when mid market goes live in 9 days." dana kept saying its an implementation problem and ravi kept saying its a product problem. and i think theyre both right and both wrong.

its an onboarding-as-a-product problem. enterprise has 14 stakeholders and our flow assumes 1. we built the flow for a legal ops manager at a 200 person company and then we just. shoved it at GCs at 10000 person companies and hoped.

if we treat onboarding as a product we can have TWO flows. mid market self serve and enterprise guided. with different milestones. different telemetry. different definition of "activated."

this would have unblocked the entire renewals conversation we had on tuesday.

BUDGET WORRY
====
ok reality check. we have like. nothing left in Q2. the AI clause extraction thing ate our eng budget and then some. priya's company had a team of 6 building this. SIX. on onboarding. i dont have 6 i have maybe 1.5 if i steal cycles from the integrations squad and dana will literally kill me.

could we do a scrappy version. like a notion-backed checklist that pretends to be in-app for v0 and we measure manually for 4 weeks. then if the metrics move we ask for real eng in Q3 planning. thats how priya said they started actually. she had one slide that just said "fake it in airtable for 6 weeks."

but also. im a PM at a CLM company not at a $40M ARR scaleup with a CFO who loves growth bets. our CFO loves gross margin. selling "lets build a thing that doesnt make money directly" is going to be a knife fight.

maybe the framing is churn reduction. if we can show that activated customers (defined as: 2 contracts signed in 14 days) renew at 94% vs 71%. thats not a feature ask thats a board slide.

actually wait do we even know the 94 vs 71 number. i made that up. we need to pull it. is that a julia question or a finance question. both probably.

SHOULD WE EVEN DO THIS
====
ok the cold shower part. im sitting here jacked up on conference energy and a $8 latte and i need to be honest. is this just shiny object syndrome.

we have committed roadmap items. the redline-v2 ga is in 5 weeks. the salesforce native integration is slipping already. and im about to walk back into the office on monday like "hey team new direction." the team is going to look at me like i grew a second head.

also. is onboarding actually our biggest lever right now. or is it AI redline accuracy. because if redline is at 78% accuracy and competitors are at 91% no amount of onboarding magic saves us.

counter to myself: maybe onboarding is the thing that lets us survive the redline gap. if customers are deeply embedded they tolerate a worse model for one cycle. moats arent always feature parity.

ok i dont know. im going to sleep on it. but im going to sleep on it loud.

PEOPLE TO GRAB
====
- julia chen. she runs CS ops and she has been quietly begging for onboarding instrumentation for like a year. she will already have half the data. coffee monday.
- marcus okafor. eng lead who half built the event spec. need to know if its salvageable or if we start from scratch. lunch tuesday before he gets pulled into the salesforce thing again.

other maybes: dana for budget reality, ravi for the enterprise flow piece, but those are second order. julia and marcus first.

ok line moved. im up. dont let me forget this on the flight home. im going to email this to myself right now so its not stuck in notes.

actually wait one more thing. the talk title was "onboarding is a product" but priya's REAL point i think was "onboarding is a contract." which for a CLM company is. you know. on the nose. we sell software that manages the agreement between two parties and we cant manage the agreement between us and our own customer about what success looks like in week one. lol. lmao even.

ok going in for my latte. brain full.
