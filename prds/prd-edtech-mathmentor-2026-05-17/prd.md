# MathMentor PRD

## Document Control

- Product: MathMentor
- Version: 0.3 (planning draft)
- Owner: Product (EdTech)
- Stakeholders: Learning Science, Trust and Safety, District Sales, Engineering, Legal/Privacy
- Date: 2026-05-17
- Status: Pre-build, scoping v1 for the 2026-2027 school year
- Distribution: Internal review only. Not for external partner sharing until Legal sign-off on the regulatory section.
- Related documents: Learning-science design rationale (forthcoming), district procurement runbook (forthcoming), COPPA verifiable-consent flow spec (in draft), Common Core mapping spec (in draft).
- Review cadence: this PRD updates monthly through build and quarterly post-launch. Material changes to the tutor's refusal behavior or the regulatory posture trigger an out-of-cycle review.

## Vision

MathMentor is an after-school, AI-powered math tutor for students in grades 3-8 (ages 8-14). The product exists for one reason: many kids hit a wall on a homework problem at 7pm, the parent cannot help (either the math has moved on since they learned it, or the parent is not home), and the kid either gives up, copies an answer from a homework-answer site, or absorbs the lesson that being stuck means being broken.

MathMentor sits in that exact moment. It does not give the answer. It asks the next question. It offers a small hint. It surfaces an analogy. It walks the student up the cognitive ladder until they reach the answer themselves, and then it logs what concept was actually shaky so the next session can revisit it.

The product is built on top of a Claude-backed conversational tutor, but the user-facing framing is never "AI tutor." The framing is "a patient guide that helps you figure it out." We hold this framing because the alternative (positioning as an AI tutor) puts MathMentor on the same shelf as the homework-answer products, and that is exactly the shelf we are trying to leave.

## Target Personas

### Learner (Maya, age 10, grade 5)

Maya is in fifth grade and is currently stuck on multi-digit multiplication using the area model. Her teacher explained it on Tuesday. Maya followed it in class but cannot reproduce it at home on Wednesday night. Her father learned long multiplication the standard-algorithm way in the 1990s and cannot help without confusing her further.

Maya does not want to feel dumb. She wants the homework done so she can go play. She has a younger brother who is already a faster reader than she is, and she is sensitive about how long things take her. MathMentor must meet Maya where she is: friendly tone, no condescension, no walls of text, and crucially, it must not make her wait so long for a hint that she gives up and copies from a sibling. Maya will use the app on a shared family iPad or a Chromebook from school.

The product's interaction style is calibrated for Maya, not for an adult learner. Sentences are short. The tutor names her, mirrors her phrasing, and assumes that she is smart and capable, never that she is broken.

### Parent (David, Maya's father)

David is the buyer. He pays $14 per month per child or $98 per year, possibly the family tier at $24 per month for up to four children. David's anxiety is twofold: he wants Maya to actually learn the material (not just finish the worksheet), and he is deeply suspicious of any product that records his ten-year-old's conversations with an AI.

David has read enough news stories about EdTech data breaches and AI training scandals to be cautious. He needs a parent dashboard that shows him what concepts Maya is mastering and where she is blocked, without surveilling the tutoring conversation itself. He needs to know the company is COPPA-compliant. He needs to know his daughter's chat transcripts are not training the model. He needs a one-click data-deletion option that he believes actually deletes the data.

If he loses trust on privacy, he cancels and tells three other parents in his school's parent Slack. The cost of a single privacy incident, in parent-network reputation, exceeds any acquisition cost we will pay for the next year.

### Teacher (Ms. Reyes, fifth-grade teacher, Chicago Public Schools)

Ms. Reyes is the school-deployed user. Her district has purchased per-seat licenses for her class of 28. She has been teaching for fourteen years, has seen dozens of EdTech tools introduced and quietly abandoned, and starts with the assumption that any new platform will waste her time.

She is not interested in reading 28 tutoring transcripts. She wants a class-aggregate view: which Common Core standards her students are mastering through MathMentor sessions outside of school, which standards are showing class-wide gaps, and which individual students appear to be struggling at home (signal for an in-class check-in, not for parent-shaming). She wants the dashboard to surface two or three actionable items per week, not a wall of metrics.

Ms. Reyes is FERPA-trained and will not touch a product that does not give her district a signed data-protection agreement. She is also fatigued by EdTech tools that bolt on dashboards no one uses, so her dashboard has to earn its weekly click. If we lose Ms. Reyes, we lose the renewal recommendation, and district renewals are how this side of the business survives.

## Capability Surface (Functional Requirements)

- Problem capture: photo-of-homework via device camera with OCR fallback to text entry. Math notation (fractions, exponents, geometry symbols) must render correctly on capture.
- Conversational tutoring loop: text-in, text-out, with optional spoken output via TTS for accessibility and for younger or dyslexic learners. Voice input is a v2 consideration, not v1.
- Hint scaffolding: small hints first (a redirected question, a definition reminder), larger hints only after a defined productive-struggle window has elapsed and the student has made at least one wrong attempt. The full answer is never offered, even on request. If the student asks "just tell me the answer," the tutor declines with a kid-appropriate explanation and offers the next-smallest hint.
- Parent dashboard: weekly digest plus on-demand view showing concepts attempted, concepts mastered, concepts flagged as shaky, total time on task, and a "what to ask your kid about at dinner" suggestion. No transcript access by default.
- Teacher / school-admin dashboard: class-aggregate view of standards coverage and mastery, individual-student flags (struggling, thriving, inactive) without exposing transcript content. FERPA-compliant role-based access.
- Progress tracking against curriculum standards: each session maps to one or more Common Core standards (with state-standard equivalents for Texas TEKS, Virginia SOL, and a small set of others in v1).
- "Stuck for too long" parent nudge: opt-in. If the student spends more than a configurable threshold (default 18 minutes) on a single problem with declining hint efficacy, MathMentor sends the parent a gentle push notification suggesting a check-in. Off by default for students 13+. The notification surfaces the concept the student is stuck on, not the conversation, and offers the parent a one-line suggestion of how to engage (a Singapore-method bar model, a real-world analogy) rather than the answer.
- Spaced retrieval review: at 1-day, 3-day, and 7-day intervals, the tutor surfaces a quick review of a previously-shaky concept at the start of a new session.
- Parent transcript escrow: by default, parents do not see the conversation. A parent may, with a documented in-product request, view a specific session's transcript. This is logged and surfaced to the student in an age-appropriate way ("your grown-up asked to see what we worked on") so the student is never surveilled silently.
- Account boundaries: each child has their own account. Family-tier accounts under one parent do not share progress data laterally between siblings, even within a family.

## Regulatory Framing

MathMentor operates in a regulatory environment more demanding than a general-purpose consumer AI app, and the product cannot pretend otherwise.

- COPPA (Children's Online Privacy Protection Act): the entire under-13 user base falls under COPPA. We obtain verifiable parental consent before account creation, restrict data collection to what is operationally required, prohibit behavioral advertising entirely, and provide parents a documented mechanism to review and delete their child's data.
- FERPA (Family Educational Rights and Privacy Act): in the school-deployed tier, MathMentor handles education records on behalf of the school. We operate as a school official under the FERPA school-official exception, sign a data-protection agreement with each district, and do not redisclose student data without district authorization.
- State student-privacy laws: explicit compliance commitments for New York Education Law 2-d (data security and breach notification, signed parent bill of rights), California SOPIPA (no targeted advertising to K-12 students, no selling of student data, no profile-building beyond educational purposes), and Illinois SOPPA (district-level contracts, public posting requirements). The product roadmap tracks a wider state matrix.
- Model training: child-level data is encrypted at rest and is not used for model training, evaluation, or fine-tuning. A separate, explicit, age-appropriate consent flow gates any future opt-in data-sharing program, and that program is opt-in for parents only (no district-level blanket opt-in).
- Data retention: tutoring transcripts are retained for 24 months by default for the purpose of progress tracking and spaced retrieval. Parents may shorten the retention window for their child's account or request immediate deletion. District-tier retention is governed by the signed data-protection agreement.
- Breach notification: we commit to breach notification timelines that meet or exceed the strictest applicable state law (currently NY Ed Law 2-d, which requires prompt notification to affected parents and the state education department).

Compliance is not a checkbox exercise here. It is the precondition for parent trust and the precondition for district procurement. We staff a privacy program lead from day one of the build, not after the first incident.

## Distribution Motion

MathMentor goes to market on two parallel motions, and they are deliberately separated in the org.

- Consumer (direct-to-parent): app store distribution (iOS, Android, web). Acquisition through parent communities, paid social aimed at parents of grades 3-8, content marketing on math-anxiety and homework-help topics, and referral credits. Pricing: $14/mo per child, $98/yr per child, or $24/mo for the family tier (up to four children). Self-serve onboarding, COPPA consent flow at signup.
- District / school sales: per-seat annual license, sold to district curriculum directors and elementary/middle-school principals. Procurement cycles align to spring planning for the following school year. Requires signed data-protection agreements, a security questionnaire response packet, and integration hooks for district SSO (Clever, ClassLink). Pricing is per-student per year, negotiated by district size with multi-year discount tiers.

These two motions share product but not playbook. The consumer flow optimizes for parent trust and time-to-first-tutoring-session. The district flow optimizes for procurement-readiness and dashboard fitness for the teacher and the curriculum director.

Sales-cycle implications: the consumer side can ship and iterate weekly. The district side cannot. District contracts are signed in spring for the following school year, and a regression in the teacher dashboard in October is a renewal risk in March. The engineering plan separates a faster consumer release train from a slower, more conservative district release train, with shared core tutor behavior governed by a single pedagogy review process.

## Pedagogy Framing

MathMentor is built on three pedagogical commitments, each grounded in published learning science. The product team includes a learning scientist on staff, and pedagogy review is a gate on every tutor-behavior change.

- Productive struggle: from research on desirable difficulty (Bjork) and Hattie's meta-analyses, students learn more from working through a problem at the edge of their ability than from being rescued early. MathMentor enforces a minimum struggle window (default 90 seconds, adjustable by grade band) before offering escalated hints. The hint sequence is modeled on a flattened Bloom's hierarchy: clarify the question, recall a relevant concept, attempt a smaller related problem, apply to the original.
- Concrete-Representational-Abstract (CRA): a long-established progression in math instruction. When a student struggles at the abstract level (symbolic math), MathMentor falls back to a representational scaffold (a Singapore-method bar model, a number line, a visual area model) before reattempting the abstract form. This is not a stylistic choice. It is an evidence-based fallback.
- Mastery feedback over completion feedback: drawing on Bloom's two-sigma observation that one-to-one tutoring with mastery-based feedback can produce a two-standard-deviation improvement over conventional instruction, MathMentor's progress signal is concept-mastery, not problems-completed. The parent and teacher dashboards reflect this.

We will cite these frameworks in our marketing only where the citation is accurate and the underlying research supports the specific claim being made. We will not invoke Bloom's two-sigma as a promise of a two-sigma outcome for any individual student. We will not claim "evidence-based" for any feature that has not been reviewed against published evidence by the learning scientist on staff.

A note on humility: published learning-science findings often fail to replicate at the scale and conditions of consumer software. We will instrument outcomes carefully (see Success Signal), publish honest results from our pilot districts, and adjust the product when the evidence says we should.

## Constraints

- Pricing (consumer): $14/mo per child, $98/yr per child, $24/mo family tier (up to four children).
- Pricing (district): per-student annual seat license, tiered by district size, negotiated.
- Latency: tutor response must arrive within 3.5 seconds at the 95th percentile, measured on a free-tier Android device on a throttled 3 Mbps Wi-Fi connection.
- Offline: the last 5 sessions (transcripts, hints surfaced, concepts touched) must be available for offline review on the student device.
- Accessibility: WCAG 2.1 AA for the student-facing surface, TTS audio output as a first-class output mode, font-size and high-contrast options.
- Platform: iOS, Android, web (Chromebook is a first-class target given school deployment).
- Localization: English-only in v1. Spanish for both student-facing tutoring and parent-facing dashboard in v2.
- Content scope (v1): grades 3-8 math, aligned to Common Core with state-standard equivalents for Texas, Virginia, Florida, and Indiana in v1, additional states staged in v1.x.
- Tutor-behavior governance: any change to the tutor's prompting, hint policy, or refusal behavior passes through the learning-science review gate before reaching production. Engineering cannot ship pedagogy changes alone.

## Non-Goals

- MathMentor is not a homework-answer service. The product refuses to give the answer, by design, every time, with no override and no premium tier that unlocks it.
- MathMentor is not a general-subject chatbot. v1 is math-only. Reading comprehension, writing, science, and social studies are explicitly deferred.
- MathMentor is not a learning management system. It does not replace Schoology, Canvas, Google Classroom, or PowerSchool. It integrates next to them.
- MathMentor is not a classroom-instruction tool in v1. It is positioned for after-school and at-home use. In-class teacher-led use is out of scope.
- MathMentor does not add social features in v1. No peer chat, no peer leaderboards, no public profiles. The risk surface (bullying, comparison, COPPA implications) is not worth the engagement bump.
- MathMentor does not gamify speed. No timed leaderboards, no streak-bonus mechanics that reward going fast over going deep. Mastery is the only reinforced loop.
- MathMentor does not run behavioral advertising. Ever. In any tier. This is a COPPA-driven hard line and also a product-identity hard line.
- MathMentor does not sell or share student data with third parties for any purpose other than narrowly-scoped service providers under signed data-protection agreements.
- MathMentor does not replace a human tutor for students who need diagnostic learning-disability evaluation. The product surfaces patterns that may suggest a student should be evaluated by a specialist, but it does not attempt to diagnose dyscalculia, ADHD, or other conditions.

## Success Signal

Success for MathMentor is learning outcomes, not engagement.

- Primary: students who use MathMentor at least 3 sessions per week for 8 consecutive weeks show a measurable improvement in concept-mastery on standards-aligned formative assessments, with a target effect size of d >= 0.3 against a matched non-using comparison group. This is measured in a controlled pilot in two partner districts in year one.
- Secondary (consumer tier): parent NPS of 40 or higher at the 90-day mark, and a renewal rate of 65% or higher on the annual plan.
- Secondary (district tier): 50% of licensed teachers actively view the class dashboard at least once per week during the school year. (A dashboard nobody opens is a dashboard that failed.)
- Guardrail: hint-to-answer-leak rate of effectively zero. Any incident in which the tutor reveals the final answer is a P1 bug and triggers a tutor-behavior review.
- Guardrail: child-distress detection true-positive escalation rate is tracked, and false-negatives (cases the system missed) trigger Trust and Safety review.
- Guardrail: dashboard-driven privacy complaints from parents are tracked and triaged as P1 if they indicate the dashboard exposed conversational content the parent had not been told it would expose.

We are deliberately not reporting time-in-app or session count as primary metrics. A product in this category can drive engagement by being addictive, and that is the wrong outcome for an eleven-year-old. If MathMentor's leading indicator is "kids spending more time on the app," we have built the wrong product.

The pilot study design (two districts, year one) is being structured with our learning-science advisor against a quasi-experimental matched-cohort design. Results, including null or negative results, will be written up and made available to district customers as part of renewal conversations. We would rather be honest about modest gains than oversell.

## Risks

A short list of risks the team is tracking, separate from open questions:

- Trust collapse on a single privacy story. The category is one bad TechCrunch article away from a 30% trial-conversion hit. Mitigation: privacy-program lead from day one, third-party audit before public launch.
- Tutor model regression on a model upgrade. A future Claude version may shift the tutor's refusal behavior or pedagogical tone. Mitigation: locked-down evaluation suite gating any model promotion to production, with the pedagogy team holding the gate.
- District procurement timeline mismatch. If the district tier is not procurement-ready by February 2027, we lose the 2027-2028 school year entirely. Mitigation: procurement-readiness checklist tracked as a release-blocker, not a nice-to-have.
- Parent-side feature creep toward surveillance. Parents will ask for transcript access. Saying yes loses the student's trust, which loses the renewal. Mitigation: documented product principle, escalation to product leadership on any exception request.

## Open Questions

- What is the right verifiable-parental-consent mechanism for v1? Credit-card-on-file is the industry-standard COPPA-compliant proxy, but it locks out unbanked families. Investigate signed-consent-form-upload as an alternative.
- How do we handle a student who is clearly working on a non-math problem (a science word problem with math embedded, a logic puzzle)? Refuse, redirect, or attempt? Pedagogy team to decide before v1 launch.
- District tier: do we build native Clever and ClassLink rostering for v1, or stage Clever for v1 and ClassLink for v1.1?
- For the "stuck for too long" parent nudge: what is the right default threshold by grade band? Need pilot data, not guesswork.
- Do we offer a school-tier-only mode where the parent dashboard is suppressed in favor of teacher visibility? Some districts will require this.
- How do we handle a student in a district-deployed tier whose family also has a consumer subscription? Identity merge is a real edge case that affects FERPA scope.
- What is the escalation path if the tutor detects language suggesting acute distress (a child expressing hopelessness, a disclosure of harm)? This is not a math product question, but it is a child-safety question, and the answer cannot be "the AI tries its best." Trust and Safety review required.
- Should we offer a low-cost or free tier for families on free-and-reduced lunch programs, and if so, how do we verify eligibility without creating a privacy-burdensome documentation flow?
- For the consumer tier, what is the right churn-prevention play when a child's grades improve and the parent rationally concludes they no longer need the product? Is "renewal because it is working" a sustainable consumer story, or do we need an explicit graduation step?
- How much of the tutor's reasoning should be visible to the learning-science reviewer for ongoing pedagogy audit, given that fully-logged reasoning traces also expand the data-protection surface for child-level data?
- For the photo-of-homework capture flow: what is our policy when a student photographs a worksheet that includes another student's name or work? OCR may incidentally capture peer data, which has FERPA implications even in the consumer tier.
- For the district tier: do we surface a parent-facing dashboard at all when the deployment is district-purchased, or do we route all visibility through the teacher? Districts differ on this, and we will likely need a per-district setting.
- What is our position on parents who want to use MathMentor as a substitute for a paid human tutor for a student with diagnosed dyscalculia? The product is not designed for that use case, but the demand will exist. Drafting a clear, kind product-positioning answer.
