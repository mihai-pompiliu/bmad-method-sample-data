# RunbookForge PRFAQ

Yes, this is an internal PRFAQ. We are using the format on purpose. If you have not seen one before: the press release is what we plan to post on #eng-announce the day this ships, and the FAQs are the questions we already know you are going to ask in the thread.

---

## Internal Press Release

### RunbookForge turns postmortem chatter into runbooks your future on-call self will thank you for

**Internal blog post, 2027-02-15**

The Platform team is rolling out RunbookForge to all engineering teams starting today. RunbookForge takes the incident notes you are already writing (Slack threads, postmortem docs, scratch pads) and turns them into structured, reusable runbooks that hook into PagerDuty and our internal ticketing system. Optional, scoped auto-execution is supported for a registered set of safe operations.

### The problem

We run about 1,400 production services across 47 squads. When something breaks at 3am, the on-call engineer rarely has a runbook. They have a wiki page from 2023 that references a dashboard that no longer exists, a Slack search that surfaces three different threads with conflicting advice, and a Confluence link that 404s.

Our internal survey from Q3 2026 showed that 71 percent of on-call engineers said "no runbook existed" or "the runbook was wrong" during their most recent page. The median time-to-mitigate for repeat incidents (same alert, fired more than once in 90 days) was 38 minutes. Most of that time is rediscovery, not remediation.

We have plenty of postmortem content. We do not have a path from "we figured it out at 3am" to "the next person who gets this page does not have to figure it out again."

### The solution

RunbookForge closes that loop. You paste in your incident Slack thread or postmortem notes. The tool extracts the diagnostic steps and remediation actions as discrete blocks. You tag the trigger conditions (for example, "Kafka consumer lag greater than 10K for 5 minutes on the orders cluster"). The result is a structured runbook that future on-call can follow step by step, or, for steps that map to our registered safe-operations library, can be partially auto-executed with explicit per-step approval.

Every runbook links to the PagerDuty service it belongs to. When the matching alert fires, the runbook surfaces in the PagerDuty incident pane and in our internal ticketing tool. No more Confluence searches.

### Engineer quote

"I wrote a runbook for the orders-consumer lag incident in eleven minutes, mostly by pasting the Slack thread. Three weeks later it fired at 2am, the new on-call followed the runbook, and we closed the incident in nine minutes. That is the entire pitch." -- Priya Ramaswamy, Staff SRE, Payments Platform

### Product overview

- Paste incident notes (Slack export, Markdown, raw text). RunbookForge extracts steps.
- Edit, reorder, and annotate every step. The AI extraction is a starting point, never the final word.
- Tag trigger conditions tied to existing alert definitions in Datadog and Prometheus.
- Register safe operations from our internal ops-library. Anything not registered is documentation only.
- Approval gates on every auto-executed step. No silent execution. Ever.
- Full audit log per runbook execution, exported to our existing observability stack.

### How to start using it

Visit go/runbookforge and sign in with SSO. Your team's existing PagerDuty services are auto-discovered, and you can attach your first runbook in under five minutes.

### Sponsor quote

"We are paying for this out of the Platform budget because we believe shaving even ten minutes off the median repeat-incident MTTM pays back the build cost in one quarter. We are not mandating adoption. We are betting that on-call engineers will choose the tool that makes 3am less miserable." -- Devon Carter, VP Engineering, Infrastructure

### Where to ask questions

Slack: #runbookforge. Office hours every Thursday 2pm Pacific. Docs at go/runbookforge-docs. File bugs in JIRA project RBF.

---

## FAQ: Engineer questions

**How is this different from our existing wiki?**
The wiki is a flat document store. RunbookForge runbooks are structured (steps, conditions, operations), they are linked to alerts so they surface automatically during an incident, and steps can optionally execute against registered safe operations. You can still write prose. You just get more than prose.

**Can I edit the AI-extracted runbook before saving?**
Yes. The extraction is a draft, not a commitment. You will see every extracted step, you can reorder them, rewrite them, delete them, merge them, or throw the whole draft away and write the runbook by hand. Nothing is saved until you hit save.

**Does this auto-execute anything without my approval?**
No. Auto-execution requires three things: (1) the step maps to a registered safe operation, (2) the runbook owner has marked that step as auto-executable, and (3) the on-call engineer running the runbook clicks approve on that step at execution time. There is no path where a step runs silently. We will refuse feature requests that try to remove the approval gate.

**What if my runbook is wrong and someone follows it?**
Same risk model as a wrong wiki page, with two improvements. First, every runbook has a "last verified" date and a one-click "this worked" or "this did not work" feedback button after execution, so stale runbooks get flagged fast. Second, runbook owners get a digest of execution outcomes weekly. We are not promising your runbook will be correct. We are promising you will find out when it is not.

**Can I use this from the terminal / CLI?**
Yes. `rbf` ships with the standard internal CLI bundle. You can list runbooks, view a runbook, attach a runbook to a PagerDuty service, and trigger an execution from your shell. Creating and editing runbooks is web-only for v1, because the editing UX is genuinely better with a real editor.

**How does it know which alerts to suggest as triggers?**
We index your team's Datadog monitors and Prometheus alert rules nightly. When you are tagging a trigger condition, the tool autocompletes against that index. You can also type a freeform condition if the alert does not exist yet.

**What is the deal with auth and permissions?**
SSO for login. Authorization is scoped by PagerDuty service ownership: if your team owns the service in PagerDuty, you can create and edit runbooks attached to it. Auto-execution permissions are a separate role, granted per service, and audited. The audit log is queryable.

**Where does the data live?**
Runbook content lives in our managed Postgres (same cluster as the ticketing tool, separate database). Execution logs go to our existing observability backend (Snowflake via the standard pipeline). Nothing goes to a third-party LLM provider. Extraction runs on our internal inference cluster against the approved model list.

**What about incident notes that contain customer data?**
Incident notes are classified as Confidential by default, same as postmortems. The extraction pipeline runs in our internal VPC, no third-party calls. Runbooks themselves are visible only to engineers in the owning team plus on-call delegates. If you paste data classified Restricted (PII, payment data, health data) the upload is blocked at the client and you get a clear error. We do not silently scrub. We refuse.

**Can I export runbooks?**
Yes, Markdown and JSON. The JSON schema is documented and stable across minor versions. We will give six months notice before any breaking schema change.

**Will this be open-sourced eventually?**
Maybe. There is interest from the SRE team in extracting the core runbook engine as an open-source library, separately from our internal integrations. No timeline. Do not block any decisions on this.

---

## FAQ: Leadership questions

**Why build instead of buy? Rootly, FireHydrant, and Transposit all exist.**
We evaluated all three in Q4 2026. Two issues. First, none of them integrate with our internal ticketing system or our safe-operations library, which is where the auto-execution value actually lives. We would be paying for the documentation layer only, which is the cheap part. Second, our data classification policy requires that Confidential incident notes stay in our VPC. Two of the three vendors could not commit to that without an enterprise contract that exceeded the three-year build cost. Buy remains on the table for v2 if our needs converge with what vendors offer.

**What headcount is required to maintain this?**
Two engineers on the Platform team, ongoing. Same team that already maintains our internal ticketing integrations and the safe-operations library, so the marginal cost is one full-time engineer plus on-call rotation overhead. We are not asking for new headcount.

**What are the success criteria?**
Three numbers at the twelve-month mark. (1) Median MTTM for repeat incidents drops from 38 minutes to under 20 minutes. (2) At least 40 percent of P1 and P2 alerts have an attached runbook. (3) At least 30 percent of weekly active on-call engineers have viewed or executed a runbook in the prior 7 days. If we hit two of three, we continue. If we hit one or zero, we hold a kill-or-pivot review.

**What if engineers do not adopt it?**
Real risk. We are not mandating use. Our mitigation is to make the path of least resistance go through RunbookForge: PagerDuty alert payloads will surface attached runbooks automatically, and the postmortem template will include a "did you write a runbook" prompt with a one-click flow. If adoption is still below the 30 percent threshold at six months we will reassess scope, not push harder on mandates.

**Why now?**
Two reasons. First, our repeat-incident rate has grown faster than our on-call rotation can absorb (up 34 percent year over year), and the cost is showing up in burnout survey scores. Second, the safe-operations library hit critical mass in Q1 2027 (over 200 registered operations across the top 50 services), which means auto-execution is finally useful enough to ship. We could have built the documentation layer a year ago. The execution layer is what makes this worth doing now.
