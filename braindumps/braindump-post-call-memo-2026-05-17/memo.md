From: Dana Reyes
To: Product
Date: 2026-05-17 11:42am
Subject: Re: Northwind QBR / renewal risk

The call ended about 20 minutes ago.

I'm writing this in the lobby of their office before I forget any of it, because I think we have a real problem and I need the leadership team to look at this today or tomorrow, not next sprint planning.

Quick context for anyone catching up.

Northwind is our $280K ARR enterprise account. Renewal is 90 days out (mid August). I went in expecting a standard QBR.

It was not a standard QBR.

Their VP of RevOps, Priya Shah, opened the meeting by saying, and I am quoting her directly because I wrote it down on my phone in the elevator on the way out:

"If we can't get manager sign-off on large campaigns by the time we renew, we're not renewing. I cannot keep explaining to our CMO why a 400K-send went out without anybody approving it."

That is not a soft signal. That is a churn statement on the record, in front of their Director of Marketing Ops and their procurement lead.

Procurement was in the room. I want to flag that twice.

Here is what they actually need, as best I can reconstruct it from the call:

- A native approval workflow for outbound campaigns. Right now they are duct-taping it together in Slack and a shared Notion page. People are bypassing it. The CMO found out last month when a campaign went to a suppression segment by mistake.
- A hard trigger threshold on audience size. They specifically said anything over 50K recipients needs to route to a designated approver before it can send. They were very specific about the number. I think the number is negotiable but the concept is not.
- A full audit log. Who requested, who approved, when, what the audience snapshot looked like at approval time, what the final send looked like. They need this for SOC2 and for their internal comms compliance review.
- Manager hierarchy awareness. The approver should be configurable per team, and ideally it pulls from their CRM (they're on Salesforce) so they don't have to maintain a parallel org chart in our tool.

I know we said we weren't going to build approval flows this year.

I was in that room. I argued against it in February when Finance brought it up for the mid-market segment. I still think we were right to push back then, because mid-market was asking for it as a nice-to-have and the spec was vague.

This is different.

This is one enterprise customer being explicit and concrete, and I think they speak for at least three or four others in our top 20 who have hinted at the same thing in less urgent language. Acme Logistics asked about it on their last EBR. Brightline Health flagged "campaign governance" as a gap in their renewal survey. The pattern is there, we just hadn't been listening for it.

I also know we said we weren't going to react to single-customer requests with a roadmap shift.

I am aware of how this email looks.

I am the PM who has been preaching "no reactive thrash" in every roadmap review for the past six months. I see the irony. I am writing it anyway because the signal is too clean to ignore: enterprise customer, hard dollar number, hard date, named competitor in the room, and a use case that is genuinely under-served in our product.

On the competitor.

Priya let it slip, and I don't think she fully meant to, that they had a demo last week with SendForge. She said their approval module "looked pretty polished, honestly." I pressed gently and she didn't deny they're in active eval.

So we are not in a vacuum here.

If SendForge can stand up a working demo in 60 days and we can't, we are going to lose this and probably a couple of adjacent accounts who talk to Northwind at the RevOps Collective dinners. That circuit is small. Word travels.

Scope, as I see it, and I want to be disciplined about this.

We are NOT building a general-purpose workflow engine. We are building campaign approval. Audience threshold trigger, configurable approver, audit log, hierarchy lookup. That is the scoped feature.

If someone tries to expand this into "approvals for everything in the platform," I will personally push back, because that is how 90-day deliveries become 9-month deliveries. I have been on the wrong side of that movie before and I am not interested in a sequel.

What good looks like, in my head:

- Northwind renews at $280K or higher in August.
- We can demo the feature working end to end by mid July (60 days), in time for their procurement review and ahead of whatever SendForge brings to the next conversation.
- Within 6 months of GA, at least two other enterprise accounts have the feature turned on and in active use. If we ship this and only Northwind uses it, we built the wrong thing.

Open questions I don't have answers to yet and want input on:

- Pricing. Is this enterprise-tier-gated, or do we make it GA across all paid plans? I lean enterprise-only for the first two quarters but I can see the argument for GA.
- Configuration depth. Do we ship with a single threshold (audience size) or do we let customers define their own trigger rules from day one? I think single threshold for v1, but I'm not married to it.
- CRM integration. Salesforce manager hierarchy lookup is non-trivial. Do we build it native, or do we punt and let customers map approvers manually for v1 and add the Salesforce sync in v1.1?

I already heard from Marcus (VP Eng) last week that approval workflows are "Q4 maybe, probably next year."

I respect Marcus. I know his team is heads-down on the deliverability rework. But I think Q4 is wrong now.

The deliverability rework matters for retention across the base, sure. Losing Northwind and the halo accounts is a faster, bigger hit than anything deliverability buys us in the same window. I want to make that case to him directly but I need air cover from product leadership first, because if I walk into his office cold he will (correctly) point out that I'm the same PM who told him in March we wouldn't be doing this.

Last thing.

Need a call on this by EOW, otherwise we lose the renewal.

I'm blocking Thursday afternoon on my calendar in case we can get the right people in a room. If Thursday doesn't work, Friday morning, but not later. Priya wants a written response from us by end of next week and I'd rather be aligned internally before I write it.

Dana
