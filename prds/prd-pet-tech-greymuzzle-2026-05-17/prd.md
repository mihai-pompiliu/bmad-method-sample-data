# GreyMuzzle PRD

**Product:** GreyMuzzle
**Category:** Consumer pet-tech (mobile app + optional connected hardware)
**Author:** Product
**Date:** 2026-05-17
**Status:** Draft, v1 scope
**Target launch:** Q1 2027 (app), Q2 2027 (collar tag)
**Platforms:** iOS first, Android four months behind

## Vision

There is a moment, somewhere between year seven and year ten, when you notice your dog has gotten old. Not sick. Just old.

The stairs take a beat longer.

They stand in the doorway and forget which way they were going. They sleep through the doorbell. They circle the water bowl twice before they drink. They lie down on the cool kitchen tile in a way they never did before.

Most of these signals are too small to mention to the vet, and most owners forget them by the time the annual visit comes around. So the vet asks "how is she doing?" and the owner says "good, I think" because that is what the heart wants to say. And a year goes by. And then another.

GreyMuzzle exists for the in-between years. It is the place an owner can quietly note what they are seeing, so that when something does shift, the shift is visible. It is not a fitness tracker for old dogs. It is a way of paying attention.

We are also not pretending this story has a different ending. Senior-dog ownership is a love story with a known last chapter. The product respects that.

## Target user

**Primary persona.** Owners of senior dogs, where "senior" is defined as age 7+ for large breeds and age 10+ for small breeds. Skews 45-75 in age. Often empty-nesters. Often have lived with this specific dog for most of the dog's life.

They are not early adopters. They are emotionally invested.

They have already started Googling "how old is my dog in human years" and finding answers they don't love. They have started watching the dog get up from the floor and counting how many tries it takes.

**Secondary persona.** Adult children who set up the app on a parent's phone, on behalf of the parent's dog. This is a real and common installation pattern. The kids buy the tag. The parent does the daily check-ins. Both feel better. The kids check in occasionally on their own phones, mostly to feel connected.

**Tertiary persona.** A small number of vet techs and vet practices who recommend the app to clients. They are not the buyer, but they are a powerful channel. A flyer in the exam room is worth a hundred dollars of ad spend.

**Not the target.** Puppy owners. Multi-young-dog households. Working-dog handlers. Breeders. Showing-circuit owners. Tactical-pet-tech buyers. The product is not built for them and we will not contort it to serve them. If a young-dog owner installs the app and bounces in week one, that is the correct outcome.

## Capability surface

- **Daily check-in.** Three questions. Under thirty seconds.

  Designed deliberately for someone who is 70 years old and tired. The questions rotate (eating, mobility, mood, bathroom, sleep) so the owner is not gaming a single metric.

  The check-in lives on the home screen and is the first thing the app asks for, every time it opens. If the owner skips a day, the app does not nag. It simply notes the skip and moves on. We are building habit, not compliance.
- **Medication schedule.** Reminders with acknowledgment. Refill warnings. Multi-medication support (a senior dog commonly has three to five medications, sometimes more). Snooze, skip, or mark-given. History view so the owner can show the vet "she has been on this twice a day since March."
- **Episode logging.** One tap to log an episode (seizure, vestibular event, collapse, vomiting, anything unusual). Optional voice note. Optional video attachment. Timestamped automatically. Location optional. This is the feature that earns the app its place on the phone. When something scary happens at 2am, the owner needs one button, not a form.
- **Mobility tracker.** Subjective owner rating on a simple scale (better, same, worse than yesterday), plus optional accelerometer baselines from the collar tag. The goal is not a step count. The goal is "is this week different than last month." We surface trends, not numbers.
- **Bathroom tracker.** One tap. Separates pee, poop, and accident. Pattern detection over time (accidents clustered at night, frequency changes, decreased water-related output, etc).
- **Quality-of-life weekly assessment.** Adapts the two widely used scales (HHHHHMM and JOURNEYS) into a weekly five-minute prompt. The score is private to the owner unless they choose to share it. We do not interpret the score for them. We do not say "your dog's QOL is low." We show the score and the trend, and we let the owner sit with it.
- **Vet-share.** Generates a clean one-page PDF summarizing the last 30, 60, or 90 days. Print it, email it, hand it to the vet at the front desk. No integration burden on the practice. The PDF is designed to be glanceable in a fifteen-minute appointment.
- **Memorial mode.** When a dog passes, the app shifts. Reminders stop. Data stays.

  The owner can export everything, or they can leave the dog's profile in place and visit it.

  This feature is quiet. It does not upsell. It does not suggest getting another dog. It does not offer cremation services. It does not push grief content. It simply holds the data and gives the owner a way to revisit it on a day they want to.

  We have heard from owners of competing products that the absence of a thoughtful end-of-life experience is one of the most painful moments in pet-tech. We will not repeat that mistake.

## Hardware companion (optional collar tag)

The tag is optional. The app is fully useful without it. We are deliberate about this because the buyer is emotional and the dog is old, and we will not gate care behind a $39 purchase.

- **BLE only.** No cellular. The tag pairs to the owner's phone and syncs when in proximity. This is a senior dog living in a senior home, not a tracker for a dog that runs off.
- **Price.** $39 one-time. No subscription required to use it. Pro is required to pair the tag to the app, but the tag itself is a one-time purchase.
- **Battery.** CR2032 button cell, replaceable, eighteen-month life. No charging. This matters. The target user is not going to remember to charge a collar tag every week. We have seen what happens to wearables that need weekly charging in this demographic. They end up in a drawer.
- **Durability.** IP67. Survives a bath, survives rain, survives the dog standing in a puddle. Not rated for swimming or submersion.
- **Sensor.** Accelerometer only. Captures activity, rest patterns, and sleep disturbance. No heart rate. No SpO2. No GPS. No microphone.
- **Sizing.** v1 fits medium-to-large dogs (25 lb and up). Small-breed tag is a future SKU.
- **Form factor.** Clips to an existing collar. We are not asking the owner to replace the collar the dog has worn for ten years.

## Vet integration

v1 integration is the PDF. The vet receives a one-page summary. No portal login. No software adoption. No friction on their side.

We believe this is the right v1 because it works for every vet practice on day one and respects the constraint that a small-animal practice cannot adopt a new SaaS for every product their clients use. The vet's day is fifteen-minute appointments, back to back, for nine hours. Anything that adds even sixty seconds of friction will be quietly ignored.

The PDF is designed with veterinary input. The top of the page is the headline (medications, weight trend, QOL trend). The middle is the episode log. The bottom is the mobility and bathroom patterns. A vet should be able to read it in under ninety seconds and walk into the exam room knowing more than they would have from the owner's recall alone.

Post-v1 we will evaluate direct ePMR integrations with the three dominant practice systems (Vetspire, ezyVet, IDEXX Neo). This is deferred and surfaced as an open question below. The integration is technically straightforward. The challenge is practice-side adoption, not engineering.

The app is not diagnostic. It does not interpret the data. It does not suggest a diagnosis. It does not recommend a treatment. The disclaimers are present and visible without being aggressive. Vet partnership programs (co-branded onboarding, practice-recommended setup, in-clinic flyers) are an opportunity post-v1.

## Constraints

- **Pricing.** App is free. Pro tier is $4/month and adds vet-share PDF generation, collar tag pairing, and multi-pet households (two or more dogs). The free tier is fully usable for one dog and includes the daily check-in, medication, episode logging, mobility, bathroom, and QOL assessment. We are not crippling the free tier. The free tier is the product. Pro adds three specific things.
- **Hardware cost.** $39 one-time. We will not subsidize through subscription lock-in. The tag works if the user cancels Pro (they lose tag-app sync features but keep the historical data).
- **Accessibility.** Large-text mode is offered in onboarding, not buried in settings. Voice input is available for episode logging because the owner may be panicked and not want to type. High-contrast mode. Minimum 16pt body text. Tap targets sized for arthritic hands. Onboarding works in landscape on a tablet, because adult children sometimes set this up on an iPad and hand it to a parent.
- **Privacy.** Pet health data is stored encrypted at rest. We do not sell data. We do not share with insurance partners. Vets see only what the owner explicitly exports. We are not building a data-broker business on top of senior dog health. The privacy policy is written in plain language.
- **What we will not do.** No advertising in the app. No upsells inside the medication reminder flow. No nudges to buy supplements. No affiliate links to "products that help with this."

  The app is paid for by Pro subscribers and tag sales, not by attention. This is a constraint we are willing to enforce against revenue pressure. If a board member or a growth lead later proposes adding sponsored content to the app, the answer is no, and the answer being no is part of the product.

## Non-goals

- Not a diagnostic AI. We will not recommend health actions or interpret symptoms. This is deliberate and permanent for v1.
- Not a vet-discovery or appointment-booking marketplace.
- Not a pet insurance product or insurance broker.
- Not a generic activity tracker for puppies or young adult dogs. A younger-dog adjacent product may exist later, but it is a separate offering.
- Not a prescription medication marketplace. Chewy and PetMeds exist and serve this need.
- Not a social network for pet owners. The product is private and personal. There is no feed. There are no friends. There are no likes on your dog.
- Not a multi-sensor biometric platform. The tag has an accelerometer. That is the only sensor. We are not adding heart-rate, SpO2, or temperature in v1.
- Not a generative-AI symptom checker. We will not let the LLM tell the owner what is wrong with their dog.

## Tension we are designing into

There is a question that will come up in every focus group. "Is this overengineering my dog's life?"

It is a fair question. People love their pets, and they are rightly skeptical of being sold a dashboard for that love.

The answer the product needs to embody is this. We are not asking you to track your dog more. We are asking you to notice your dog more carefully, and to have a place to put what you notice.

The check-in is thirty seconds. The episode log is one tap. The QOL assessment is once a week. The collar tag has an eighteen-month battery and no app of its own. There is no scoreboard. There is no leaderboard. There is no streak.

If the product is overengineered, we have failed.

The owner should close the app feeling like they did something kind, not like they completed a chore.

## Success signal

- **Owner retention.** 70% of paying (Pro) owners are still active in the app six months after their dog's age 9 birthday. This is the metric that matters. Senior-dog owners drop tools that feel like work. They keep tools that feel like care.
- **Vet-share usage.** 40% of users generate at least one vet-share PDF within their first 90 days.
- **Tag attach rate.** 25% of free-app users purchase a collar tag within six months of installation.
- **Qualitative resonance.** Owner reviews skew toward "this helped me see what was happening" and away from "fitness tracker for dogs." We will read the reviews. The language matters more than the star rating.
- **Bereaved-user behavior.** A non-trivial fraction of users keep the app installed for at least 30 days after entering memorial mode. This is not a vanity metric. It tells us we built the end-of-life experience with the right tone.

## Open questions

1. Veterinary EMR integration roadmap. Do we pursue Vetspire, ezyVet, and IDEXX Neo in that order, or wait for inbound vet demand to signal which to prioritize?
2. Small-breed tag SKU. When, not if. The target user owns a small dog roughly half the time, and the v1 tag excludes them.
3. End-of-life support. Memorial mode is in scope, but is there a deeper offering (grief resources, paw-print mementos, vet bereavement coordination) that fits the brand without feeling commercial?
4. Family-account model. Adult children installing on behalf of a parent is common. Do we offer a "caregiver" role that shares visibility without disrupting the parent's primary ownership of the data?
5. Insurance partnerships. We have ruled out being an insurance product. Is there a referral or discount partnership (Trupanion, Healthy Paws, Pumpkin) that respects the privacy posture and benefits owners?
6. Tag v2 sensor expansion. We have ruled out heart-rate and SpO2 for v1. Is there a quiet case for ambient temperature (detecting overheating in a dog that no longer regulates well) in a future revision?
7. International. v1 is US-only. UK and Canada are obvious next markets but the medication regimen prompts and QOL scales may need light localization.
8. Cat version. We will get asked. The answer for now is no, but the architecture should not preclude it.
9. Boarding / dog-sitter mode. When a senior dog stays elsewhere for a weekend, does the sitter get a temporary, scoped view of the medication schedule and the daily check-in prompt? This feels valuable but is not v1.
10. Apple Health and Google Fit. Do we surface activity baselines into the owner's own health platforms in a meaningful way, or is that outside the brand?
11. Watch app. Apple Watch and Wear OS would make medication acknowledgment one-tap from the wrist. The build cost is non-trivial. Worth scoping for v1.5.
12. Multiple senior dogs in one household. Pro covers this technically, but the emotional flow (one dog ages faster than the other, one passes first) needs design care.
