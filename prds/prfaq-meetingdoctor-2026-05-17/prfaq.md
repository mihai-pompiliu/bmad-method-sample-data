# MeetingDoctor PRFAQ

Internal draft. Audience: Acme leadership, People Ops, IT Security, and the Workplace Productivity working group.

Last updated: 2026-05-17

---

## Press Release (Internal)

**FOR INTERNAL DISTRIBUTION**
**Bellevue, WA. May 17, 2026.**

### Acme Releases MeetingDoctor: An Internal Tool That Returns Time to Managers

Acme today announced the general internal availability of MeetingDoctor, a new productivity tool that helps managers reclaim the single most contested resource in the company: their calendar. Built by the Workplace Productivity team and dogfooded across Engineering, Sales, and Finance over the last six months, MeetingDoctor analyzes the last 30 days of a manager's calendar and Zoom recording metadata, then recommends specific meetings to kill, shorten, or combine. Content is never read. Only metadata, such as attendee counts, recurrence patterns, duration, acceptance rates, and recording length, is used.

Most managers at Acme spend between 22 and 31 hours per week in meetings, and internal surveys consistently rank "too many meetings" as the top blocker to deep work. Previous attempts to address this, including no-meeting Wednesdays and meeting-cost calculators, produced short-lived improvements but no durable change. MeetingDoctor takes a different approach. Instead of asking managers to audit their own calendars, it does the audit for them and presents a ranked, explainable set of recommendations each Monday morning. Managers can accept, modify, defer, or reject each one with a single click. Nothing is changed without the manager's explicit approval.

During the beta, 412 managers used MeetingDoctor for at least four consecutive weeks. On average, those managers reclaimed 4.7 hours per week of previously committed meeting time. Eighty-one percent of accepted recommendations were still in effect 30 days later, suggesting the changes stuck rather than reverting. Recurring meetings with declining acceptance rates, low attendance from the originally invited group, or zero recording engagement were the most common candidates for removal.

"We did not build this to surveil anyone, and we built guardrails to make sure it never becomes that," said Priya Raman, VP of Workplace Experience at Acme. "MeetingDoctor exists because our managers told us, over and over, that they wanted help. They wanted a thoughtful second opinion on their own calendar. That is what this tool is. It is a coach, not a cop."

Managers in the beta echoed that framing. "I had a recurring sync that everyone hated and nobody would kill, because nobody wanted to be the one to kill it," said David Okafor, a senior engineering manager who joined the beta in February. "MeetingDoctor flagged it on week one with the reasoning right there. Eighteen attendees, acceptance dropping every week for three months, recording watched by zero people. I forwarded the recommendation to the group, we agreed in five minutes, and that hour came back to all of us. I have had four similar wins since."

MeetingDoctor is available today to all people managers at Acme through the internal tools portal. Enrollment is opt-in, recommendations are private to the manager by default, and aggregate metrics are reported only at the org level with a minimum cohort size of 25. Individual calendar data is never shared with HR or with a manager's leadership chain. The roadmap for the second half of 2026 includes team-level recommendations, a Slack digest, and integration with the Acme onboarding flow for new managers.

---

## Frequently Asked Questions

### Customer (Manager) FAQs

**1. What exactly does MeetingDoctor look at?**

It looks at metadata from your Google Calendar and Zoom account for the last 30 rolling days. Metadata means meeting titles, attendee lists, duration, recurrence rules, acceptance and decline rates, whether recordings exist, and recording view counts. It does not read meeting notes, chat transcripts, recording audio, recording video, or any document attached to the invite.

**2. How is my privacy protected?**

Three ways. First, the analysis runs in your own tenant on data you already have access to, and the output is visible only to you. Second, no individual calendar data ever leaves your view. Aggregate metrics roll up only at the org level and only when at least 25 managers are in the cohort. Third, HR and your leadership chain do not have access to your MeetingDoctor recommendations, accepted actions, or history. This is enforced by access controls reviewed by Security and documented in the internal privacy memo linked from the tool.

**3. What if I disagree with a recommendation?**

You reject it. There is a one-click reject button, and you can optionally add a reason. The tool learns from your rejections and will weight similar future recommendations lower. You are never required to act on anything. The goal is a useful second opinion, not a mandate.

**4. Will my manager or HR see what MeetingDoctor recommends about me?**

No. Your recommendations and your accept or reject history are private to you. The only thing visible above your level is anonymized, aggregated org-level metrics, and only when the cohort is large enough that no individual can be identified.

**5. What happens to recurring 1:1s?**

MeetingDoctor treats 1:1s as a protected category by default. It will not recommend killing or shortening any meeting flagged as a 1:1, which is detected by attendee count of two plus recurrence. It may, in rare cases, suggest changing cadence (for example, biweekly instead of weekly) if acceptance has dropped sharply, but only as a soft suggestion with the protected-category warning visible.

**6. Does it look at meetings I do not own?**

It analyzes meetings on your calendar, including ones you do not own, because your time matters regardless of who scheduled it. However, recommendations to kill or change a meeting you do not own are framed as "draft a note to the organizer" rather than "delete this meeting." You cannot delete other people's meetings, and the tool will not pretend you can.

**7. What if a meeting looks low-value by the metadata but is actually critical?**

That is the most common false positive, and we expect it. Every recommendation shows the reasoning, including the specific signals that triggered it. If a meeting is critical for reasons the metadata cannot see, reject the recommendation. Your reject teaches the model for next time. We would rather show you a wrong recommendation you can dismiss in two seconds than miss a real one.

**8. Can I use this if I am not a people manager?**

The first release is gated to people managers because they have the most calendar control and the highest meeting load. Individual contributor support is on the roadmap for Q4 2026, pending learnings from the manager rollout.

### Internal and Stakeholder FAQs

**9. How will we measure success?**

Three primary metrics. First, hours reclaimed per active manager per week, measured by subtracting post-action committed meeting time from pre-action committed meeting time over a four-week window. Target is three hours per manager per week by end of Q3. Second, 30-day persistence of accepted recommendations, with a target of 70 percent or higher. Third, manager-reported satisfaction in the quarterly engagement survey, specifically the "I have enough time for focused work" question, with a target of a five-point lift among MeetingDoctor users versus non-users.

**10. Why are we using only metadata and not meeting content?**

Two reasons, and they are non-negotiable. First, privacy and trust. The moment we read transcripts or recording content, MeetingDoctor stops being a productivity coach and starts being surveillance, regardless of intent. We would lose the room before we shipped. Second, signal quality. Beta results showed that metadata alone, especially acceptance rate trends, recording view counts, and attendee-to-active-participant ratios, was sufficient to surface the meetings managers themselves agreed were waste. Content analysis would add cost, risk, and review burden for marginal lift.

**11. Why build versus buy?**

We evaluated four commercial options during planning. Three required granting full content access to a third party, which Security rejected. The fourth was metadata-only but priced per seat at a level that exceeded a three-year build-and-operate estimate, and it could not be tuned to Acme's specific meeting culture (for example, our protected 1:1 category and our org-chart-aware aggregation). Building also gave us full control over the privacy model, which we consider the core feature, not a wrapper around someone else's logic.

**12. How do we avoid this becoming HR surveillance?**

Three structural commitments, written into the tool's design doc and reviewed by Legal. First, individual data is never exposed to anyone other than the manager whose calendar it is. There is no admin view of "show me Jane's recommendations." Second, aggregated metrics require a minimum cohort of 25 and are scoped to org level, never team level. Third, MeetingDoctor outputs are explicitly excluded from performance review inputs in the People Ops policy. If any of these three are ever weakened, the tool should be sunset. We have committed to this in writing.

**13. What is the rollback plan if adoption craters or trust erodes?**

Enrollment is opt-in and reversible. A manager can disable MeetingDoctor at any time, and disabling deletes their stored recommendation history within seven days. At the program level, if active usage drops below 20 percent of enrolled managers for two consecutive months, or if the quarterly trust survey shows a net negative shift, we pause new enrollment and run a structured review with People Ops and Security before continuing.

**14. What does this cost to operate?**

The build was 2.3 engineer-years. Ongoing operating cost is approximately 90,000 dollars per year, dominated by compute for the weekly batch analysis and on-call support. At current beta-derived productivity gains, the breakeven is roughly 38 managers actively using the tool. We are well past that.

**15. Why now, and why us?**

The post-2024 shift to longer recurring meetings, combined with the Zoom recording explosion, gave us a metadata surface that simply did not exist three years ago. Recording view counts in particular turned out to be the single strongest "kill this meeting" signal in the beta. We are also better positioned than a vendor to ship this responsibly because we know our culture, our org structure, and our managers personally.

**16. What is on the roadmap after launch?**

Three things in priority order. First, a Slack digest so managers do not have to visit the tool to see recommendations. Second, team-level recommendations that a manager can share with their team for joint decisions (still no upward visibility). Third, integration with the new-manager onboarding flow so first-time managers start with a healthy calendar baseline rather than inheriting one.

**17. What are the known risks we are accepting?**

Three. First, false positives on meetings that are valuable for non-metadata reasons, mitigated by transparent reasoning and one-click reject. Second, the perception risk that this is surveillance, mitigated by the structural commitments in question 12 and by an internal comms plan that leads with privacy. Third, the risk that managers act on recommendations without consulting their teams, mitigated by recommendation copy that explicitly suggests team discussion before killing recurring meetings with more than four attendees.

**18. Who do I contact with questions or concerns?**

The MeetingDoctor team channel is #meetingdoctor in Slack. For privacy or security concerns specifically, contact the Workplace Privacy lead directly. For policy concerns about how MeetingDoctor interacts with performance management, contact People Ops. All three contacts are listed on the tool's landing page.
