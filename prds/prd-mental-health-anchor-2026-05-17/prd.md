# PRD: Anchor

## Document control

- Product: Anchor
- Category: Consumer wellness, journaling
- Owner: Product (consumer)
- Status: Draft for internal review
- Date: 2026-05-17
- Reviewers: Clinical advisory team (three licensed mental-health professionals, named in launch press kit), Legal, Privacy, Engineering
- Note on this document: Written with care. Every claim here has been weighed against the responsibility that comes with operating in this category.

## Vision

Anchor is a daily journaling tool for adults who carry anxiety as part of their everyday life and want a quiet, private, evidence-informed place to think on paper. It is a tool, not a treatment. It exists to help people notice their own patterns, get a little distance from anxious thoughts, and end the day feeling slightly more grounded than they started it. It does this through three short, user-chosen practices and a deliberately small AI surface that observes but never prescribes.

The product we are building is the one we wish existed for ourselves and the people we love. That is the bar.

## Target user

Anxious adults. Specifically: people who experience meaningful day-to-day anxiety, who are functioning, who may or may not be in therapy, and who want a private space to process their thoughts.

We are intentionally not using "people with mental illness" framing. The people we serve are whole adults navigating a hard but human thing. Many of them already have a clinician. Some do not, and that is their choice. Anchor does not position itself as filling that gap.

Typical user signals:
- Late-20s to mid-50s
- Reflective, often reads, often journals on paper or in Notes already
- Wants the practice but not the friction
- Privacy-sensitive (often a precondition, not a preference)
- Skeptical of wellness apps that overpromise

## Clinical boundary statement

This is load-bearing. Read it carefully.

- Anchor is a journaling tool. It is not therapy.
- Anchor does not provide clinical advice. We do not diagnose, treat, cure, or prevent any condition.
- Anchor is not a substitute for professional care. If a user is considering therapy, our position is clear: see a clinician.
- Anchor does not respond in crisis. When a user types high-risk language, the app surfaces crisis resources (988 Suicide and Crisis Lifeline in the US, Crisis Text Line, and regional equivalents based on locale). It does not engage in a conversation about the user's safety, because it is not qualified to.
- Anchor is not marketed as treating any condition. Our marketing review process (clinical advisory team sign-off on all consumer-facing copy) exists to enforce this.

What we do instead: we provide a quiet structured space, three light practices drawn from frameworks with research behind them, and zero pressure. We give users a place to think without a product trying to nudge them toward a particular outcome.

We have written this section first, before capabilities, on purpose. Every product decision below is downstream of these constraints, and where a product decision would conflict with this boundary, the boundary wins.

## Capability surface

- Daily prompt picker. Each session, the user picks one of three practices: thought-record, values check, or gratitude. No streak required.
- Thought-record (CBT-adjacent). Four fields: what happened, what I thought, what is the evidence, a more balanced reframe. Optional AI-assisted reframe suggestion, which the user always edits before saving. The AI suggestion is framed as a draft, not advice.
- Values check (ACT-informed). During onboarding, the user picks three to five personal values (examples: honesty, family, craft, health, generosity). The practice asks: given this situation, what would my values say. The app does not interpret the answer.
- Gratitude log. One line. Plain. No prompts to elaborate, no AI rewrite, no "great choice" affirmation.
- Weekly reflection. Opt-in. Surfaces the week's entries for the user to read back, with no scoring and no AI commentary unless the user has enabled the pattern surface.
- AI pattern surface. Opt-in. The AI can observe patterns in the user's own writing over time (example phrasing: "you have mentioned work pressure on three Mondays this month"). Always framed as the AI's read, never as fact. The user can dismiss or disable at any time.
- Crisis-resource surface. Always available, one tap from any screen. Not buried in settings.
- Export your data. Markdown and PDF. Any time. No questions.
- Delete your data. Instant. No retention period, no "we will keep it for 30 days in case." Gone.

## Crisis-resource handling

Anchor is not a crisis service. We want to be explicit about what we do and do not do here, because this is the area where consumer apps most often overreach.

What we do:
- A rule-based plus simple-model layer scans user input for high-risk language (suicidal ideation, self-harm phrasing, language of imminent danger).
- When detected, Anchor interrupts the journaling flow with a calm, non-alarming card that surfaces crisis resources for the user's locale: 988 Suicide and Crisis Lifeline (US), Crisis Text Line, regional hotlines, and a link to find local emergency services.
- The card includes a single sentence: "If you are in danger or thinking of hurting yourself, please reach out to one of these. Anchor is not a crisis service."
- The user can dismiss the card and continue journaling. We do not gate the app.

What we do not do:
- We do not "detect mental illness." We do not frame this feature as detection.
- We do not contact emergency services on behalf of the user. Anchor does not surveil.
- We do not log per-user crisis-card triggers in a way that could be subpoenaed against the user. Aggregate counts only, no user identifiers.
- We do not attempt to talk the user down. The app is not qualified to. Resources, not conversation.

## AI usage and limits

AI is a small, optional, opt-in part of Anchor. The default state is no AI involvement.

Where AI is used:
- Optional reframe suggestion in the thought-record (user edits before saving).
- Optional pattern surface in weekly reflection (user enables, user can disable).
- Crisis-language detection (rule-based with a small model assist; this layer is not "AI features," it is safety infrastructure).

Where AI is deliberately not used:
- AI never tells the user what to do. There is no "you should" surface in this app.
- AI never offers therapy-style interventions. No "have you considered" framings, no CBT-coach roleplay, no advice column tone.
- AI does not diagnose, label, or characterize the user's mental state.
- AI does not generate the user's gratitude entries or values answers. Those are the user's words.

Processing:
- User chooses local processing (on-device model where feasible) or anonymized cloud processing.
- Cloud processing strips identifiers before send, processes, returns, retains nothing.
- We do not train models on user content. This is a hard line and is restated in the privacy policy and the in-app onboarding.

## Evidence base

Anchor draws on practices that have research behind them. We are careful not to overstate.

- Thought-records are a core technique in cognitive behavioral therapy (CBT). There is a substantial peer-reviewed literature supporting CBT for generalized anxiety. We are not delivering CBT. We are providing a structured form that borrows the shape of a CBT thought-record, which a user can fill in on their own or with a clinician.
- Values clarification and cognitive defusion are central to acceptance and commitment therapy (ACT). Again, we are not delivering ACT. We are providing a prompt informed by the framework.
- Gratitude journaling has a smaller but real evidence base for subjective well-being effects. We do not claim mood treatment.

All of this is framed honestly in onboarding and in our public-facing FAQ. We do not cite studies in marketing copy to make therapeutic claims.

## Privacy posture

The most sensitive personal data a person can write down is what they think when they are scared. We treat it that way.

- End-to-end encryption. Content is encrypted client-side. The key is derived from a user passphrase that the company never sees. We cannot read user entries, even if compelled. We will say so publicly and in subpoena responses.
- Optional cloud sync is encrypted end-to-end. Local-only mode is a first-class option and is selected during onboarding.
- No analytics on content. Ever. Aggregate product analytics (screen views, feature adoption) only, and the user can opt out of even that.
- No third-party trackers. No Meta SDK. No Google Analytics. No Mixpanel on content. The SDK list is published.
- HIPAA-equivalent posture. Anchor is not a covered entity under HIPAA. We hold ourselves to that standard anyway as a defensive privacy posture.
- Annual independent privacy audit, results published. If we fail the audit, we publish that too.

## Constraints

- Pricing: 7 USD per month or 54 USD per year. Free tier includes daily prompt and basic logging. AI features and pattern surface are paid only.
- Platforms: iOS and Android at launch. Feature parity.
- No web app in v1. Mobile is the entire surface. Web is reconsidered post-launch based on user demand, not assumed.
- All consumer-facing marketing copy is reviewed by the clinical advisory team before publication. This includes App Store copy, website, ads, social. No exceptions.
- App Store and Play Store category compliance: we list under Health and Fitness, not Medical. We do not claim medical functionality.

## Non-goals

- Not a therapy app. Not a teletherapy marketplace. Not a coach-matching service.
- Not a meditation app. We respect Calm and Headspace as a different category. We are not competing there.
- Not a mood tracker as the primary surface. Mood may be captured as optional side data on an entry, but the loop is journaling, not mood scoring.
- Not selling user data. Not now, not later, not under acquisition (a data-protection clause survives change of control in our terms).
- Not building couples, family, or shared-journal features in v1. Privacy model gets harder fast; we are not ready.
- Not training models on user content. Restated.
- Not adding gamification. No streaks, no badges, no "you missed a day" guilt. Users in low periods do not need streak-shame from an app that claims to support them.

## Success signal

- Retention: 40 percent of paid users still active 90 days after subscription start. We will publish this number annually.
- Qualitative self-report: in optional in-app surveys, users describe the app in terms close to "this makes my day feel a little more grounded." We are measuring vibe, honestly, because the goal is vibe.
- Privacy incident count: zero. Table stakes. A non-zero number here triggers a public post-mortem and a board-level review.
- Crisis-resource surface engagement: tracked at aggregate only (no per-user data). We want to know what percentage of users who see the card tap through to a resource. We will not know who they are.

## Open questions

- Pricing in non-US markets. 7 USD is wrong in INR, BRL, NGN contexts. Localized pricing study before launch.
- Whether to offer a one-time lifetime purchase option for users uncomfortable with subscriptions in this category. Sentiment in early interviews suggests yes; revenue model is unclear.
- Apple Health and Google Fit integration: tempting (mood, sleep correlation) and also a privacy expansion. Current lean is no in v1.
- Clinician-facing export. Some users have asked for a "share with my therapist" export. Possible v2. Requires careful design so it does not become a backdoor into the encrypted store.
- Languages at launch. English only in v1, with Spanish and Portuguese as fast-follows. Translation of clinically adjacent copy must go through the advisory team in-language, which is a hiring question.
- How we handle App Store reviewer pressure to add a "talk to someone now" in-app chat. Our answer is no. The framing of that answer in App Review communications needs to be drafted in advance.
- Whether the AI pattern surface should be available on the free tier in a reduced form. Argument for: the most privacy-respecting users may be on free and could benefit. Argument against: it complicates the message that AI is opt-in and paid. Leaning against for v1.
- Whether to publish anonymized aggregate research from the app (with explicit opt-in only) as a public good. The category needs honest data. We are not the right team to lead this in v1, but it is on the longer roadmap.
