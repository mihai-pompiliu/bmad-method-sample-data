From: Derek Lin <derek.lin@northbeammetrics.com>
To: Priya Shah <priya.shah@northbeammetrics.com>, Marcus Webb <marcus.webb@northbeammetrics.com>
Cc: Jana Olofsson <jana.olofsson@northbeammetrics.com>
Subject: ROI calculator idea
Date: Mon, Apr 27, 2026, 4:12 PM

Priya, Marcus,

Throwing this out before QBR prep eats my week. We are losing late-stage deals to Plumeline. Three in Q1 I saw, probably more I did not. Every loss debrief says the same thing: their AE walks the prospect through an ROI tool, plugs in their numbers, shows 4x return over 12 months. Our reps email a PDF case study and a Loom. Knife to a calculator fight.

Proposal: build an internal ROI calculator. Web-based, on our domain, sales walks the prospect through it during the second demo. Inputs: current stack, current spend, current performance. Output: projected return if they switch to us.

Product builds, Sales drives requirements, CS sanity-checks the projection logic.

Derek Lin, VP Sales, Northbeam Metrics, (415) 555 0142

---

From: Tomas Reilly <tomas.reilly@northbeammetrics.com>
To: Derek Lin <derek.lin@northbeammetrics.com>
Cc: Priya Shah <priya.shah@northbeammetrics.com>, Marcus Webb <marcus.webb@northbeammetrics.com>, Jana Olofsson <jana.olofsson@northbeammetrics.com>
Subject: Re: ROI calculator idea
Date: Mon, Apr 27, 2026, 6:48 PM

Derek.

Numbers first. What is the average projected ROI we would publish, and what is the realized ROI across our top quartile, top half, bottom half, at month 12? Anything more than 1.4x the realized median, we are setting up a churn cliff in renewal year one.

Plumeline can promise 4x because Plumeline is privately held and does not have a CFO explaining a churn spike to a board.

I am not opposed. I am opposed to a number on a webpage I cannot defend in a deposition.

Tomas Reilly, CFO

---

From: Priya Shah <priya.shah@northbeammetrics.com>
To: Tomas Reilly <tomas.reilly@northbeammetrics.com>, Derek Lin <derek.lin@northbeammetrics.com>
Cc: Marcus Webb <marcus.webb@northbeammetrics.com>, Jana Olofsson <jana.olofsson@northbeammetrics.com>
Subject: Re: Re: ROI calculator idea
Date: Tue, Apr 28, 2026, 9:02 AM

All,

Catching up on this from last night. A few things before we decide who owns what.

The shape of this tool matters more than whether we build it. I am hearing three different products in one thread.

One: a single generic calculator. Cheap, dangerous, because a DTC apparel brand and a B2B SaaS marketer have wildly different baseline economics and a one-size projection will be wrong for both.

Two: a vertical-aware calculator. Same engine, but multipliers, input prompts, and baselines change by industry. More work, but the only version I would put my name on.

Three: a configurator that asks twelve questions per industry and produces a forty-page report. Hard pass, we do not have the design bandwidth and the sales motion does not want it either.

Before I staff anything: prospect-facing (prospect logs in and plays) or sales-rep-facing (AE drives it on a screen share)? Different products. First needs polish, auth, privacy review. Second needs to be fast to iterate on and not much else.

Tomas, "defensible" and "useful in a sales conversation" are in tension and we should be honest about that instead of pretending otherwise.

Priya Shah, Head of Product

---

From: Jana Olofsson <jana.olofsson@northbeammetrics.com>
To: Priya Shah <priya.shah@northbeammetrics.com>, Tomas Reilly <tomas.reilly@northbeammetrics.com>, Derek Lin <derek.lin@northbeammetrics.com>
Cc: Marcus Webb <marcus.webb@northbeammetrics.com>
Subject: Re: Re: ROI calculator idea
Date: Tue, Apr 28, 2026, 7:14 AM (London)

Morning all (evening for most of you).

Picking this up before standup. Priya, strong plus one on vertical-aware. From CS: whatever number gets shown to a prospect becomes the implicit success bar in the first QBR. Right now those numbers live in an AE's head or a one-off slide. By the time the customer reaches me, the goalposts have moved. We reverse-engineer what was promised from Gong recordings. Not scalable.

If we build this, the projection gets captured as a structured artifact at close, handed to the CSM, used as the first-90-day baseline. Otherwise we are just building a slicker sales pitch and the post-sale value realization gap stays where it is.

Also: please do not call it a "calculator." Calculators imply precision. We are doing scenario modeling.

Jana Olofsson, Director Customer Success, London office

---

From: Marcus Webb <marcus.webb@northbeammetrics.com>
To: Jana Olofsson <jana.olofsson@northbeammetrics.com>, Priya Shah <priya.shah@northbeammetrics.com>, Tomas Reilly <tomas.reilly@northbeammetrics.com>, Derek Lin <derek.lin@northbeammetrics.com>
Subject: Re: Re: ROI calculator idea
Date: Tue, Apr 28, 2026, 10:31 AM

team, quick from the field, in a deal cycle today:

- love the energy, hate the scope creep already
- my reps needed this last week
- a slick excel template would be fine for v1. branded, locked cells, three tabs
- plumeline's tool is not magic, it is theater. the magic is they HAVE one and we don't
- if we wait for vertical-aware + CS handoff + CFO-approved multipliers we ship in q4, lose six more deals
- counter: excel v1 in two weeks, web in q3

not torpedoing the vision, trying to stop the bleeding

marcus webb, sales director mid-market, mobile (628) 555 0177

---

From: Priya Shah <priya.shah@northbeammetrics.com>
To: Marcus Webb <marcus.webb@northbeammetrics.com>
Cc: Jana Olofsson <jana.olofsson@northbeammetrics.com>, Tomas Reilly <tomas.reilly@northbeammetrics.com>, Derek Lin <derek.lin@northbeammetrics.com>
Subject: Re: Re: ROI calculator idea
Date: Tue, Apr 28, 2026, 11:48 AM

Marcus, two pushbacks on Excel v1.

Every Excel template we have shipped gets forwarded to procurement, opened on a phone, breaks the formatting, and becomes an artifact we cannot retract or update. The version-control tax alone we pay for years.

If the goal is theater, a web surface IS the theater. Plumeline did not win those deals because their math is better. They won because their AE pulled up a clean URL on a screen share and the prospect thought "these people have their act together."

I will commit to a two-week web prototype if you commit to four inputs, one output, one vertical.

Priya

---

From: Derek Lin <derek.lin@northbeammetrics.com>
To: Priya Shah <priya.shah@northbeammetrics.com>, Marcus Webb <marcus.webb@northbeammetrics.com>
Cc: Jana Olofsson <jana.olofsson@northbeammetrics.com>, Tomas Reilly <tomas.reilly@northbeammetrics.com>
Subject: Re: Re: ROI calculator idea
Date: Tue, Apr 28, 2026, 12:15 PM

Priya, hold on. Four inputs is too few. My reps need at minimum: current ad spend, current CAC, current LTV, current attribution model, channel mix, team size, and a baseline conversion rate. That is seven. Plus vertical, which you said is gated separately. Call it eight.

Tomas, on your loss-aversion point, I agree in principle. Counter: if we publish a "range" instead of a single number, the conservative end is your shield and the optimistic end is my sword. Same tool, both jobs done.

Derek

---

From: Derek Lin <derek.lin@northbeammetrics.com>
To: Sarah Kwan <sarah.kwan@northbeammetrics.com>
Subject: Fwd: Re: Re: ROI calculator idea
Date: Tue, Apr 28, 2026, 12:22 PM

Sarah, FYI. This is the conversation I mentioned in our 1:1. Going to need marketing's input on positioning once we land on a shape. Do not respond on the main thread, just wanted you in the loop early. Will pull you in when we have something concrete.

D

---

From: Tomas Reilly <tomas.reilly@northbeammetrics.com>
To: Derek Lin <derek.lin@northbeammetrics.com>, Priya Shah <priya.shah@northbeammetrics.com>
Cc: Marcus Webb <marcus.webb@northbeammetrics.com>, Jana Olofsson <jana.olofsson@northbeammetrics.com>
Subject: Re: Re: ROI calculator (now: focused proposal)
Date: Wed, Apr 29, 2026, 8:03 AM

Renaming, this thread wandered.

Derek, range idea acceptable. Conditions:

1. Both ends visible always. No collapsing to "expected value."
2. Conservative end computed from our realized median for that vertical, not aspirational.
3. I sign off on multipliers per vertical before they go live. Real review, not rubber stamp.
4. Any post-launch multiplier change goes through me again. No silent updates.

If those hold, I am a yes on the web version. Excel does not survive my four conditions.

Tomas

---

From: Jana Olofsson <jana.olofsson@northbeammetrics.com>
To: Tomas Reilly <tomas.reilly@northbeammetrics.com>, Derek Lin <derek.lin@northbeammetrics.com>, Priya Shah <priya.shah@northbeammetrics.com>
Cc: Marcus Webb <marcus.webb@northbeammetrics.com>
Subject: Re: Re: ROI calculator (now: focused proposal)
Date: Wed, Apr 29, 2026, 7:22 AM (London)

Adding a fifth condition from CS:

5. The final projection range shown to the prospect is captured (timestamp, vertical, inputs, output range) and written to the CRM opportunity record. At deal close the CSM pulls that record, the conservative end becomes the customer's stated first-90-day goal. If the CSM cannot pull it, we did not build this right.

Without this it is a sales tool. With it, a value realization tool that starts in sales.

Jana

---

From: Heath Banerjee <heath.banerjee@northbeammetrics.com>
To: Derek Lin <derek.lin@northbeammetrics.com>, Priya Shah <priya.shah@northbeammetrics.com>, Tomas Reilly <tomas.reilly@northbeammetrics.com>, Jana Olofsson <jana.olofsson@northbeammetrics.com>, Marcus Webb <marcus.webb@northbeammetrics.com>
Subject: Re: Re: ROI calculator (now: focused proposal)
Date: Wed, Apr 29, 2026, 11:42 PM

hey all sorry joining late, derek looped me in verbally. catch me up: ecom + b2b saas for v1 or just one? and is this prospect facing or rep driven, i saw it asked but did not see the asnwer.

fwiw on excel, the forecasting template from 2024 is STILL in circulation despite three deprecation notices

heath banerjee, revops lead

---

From: Priya Shah <priya.shah@northbeammetrics.com>
To: Heath Banerjee <heath.banerjee@northbeammetrics.com>
Cc: Derek Lin <derek.lin@northbeammetrics.com>, Tomas Reilly <tomas.reilly@northbeammetrics.com>, Jana Olofsson <jana.olofsson@northbeammetrics.com>, Marcus Webb <marcus.webb@northbeammetrics.com>
Subject: Re: Re: ROI calculator (now: focused proposal)
Date: Thu, Apr 30, 2026, 9:11 AM

Heath, welcome in.

Quick catchup:

- Web-based, not Excel. Embeddable, presented in the demo by a rep on a screen share.
- Output is a range (conservative to optimistic), both ends visible. No single hero number.
- Vertical-aware on a shared engine. Multipliers per vertical, CFO sign-off on each.
- Max eight inputs. Derek's seven plus vertical is the starting point.
- CS gets the projection artifact at deal close, conservative end becomes the customer's first-90-day baseline.
- Tomas owns multiplier governance.

Open: prospect-facing or rep-facing surface. I am leaning rep-facing for v1 (faster, no auth, no privacy review, no public exposure of our economics). Reply-all if you disagree, otherwise rep-facing v1 and we move on.

Priya

---

From: Marcus Webb <marcus.webb@northbeammetrics.com>
To: Priya Shah <priya.shah@northbeammetrics.com>
Cc: Heath Banerjee <heath.banerjee@northbeammetrics.com>, Derek Lin <derek.lin@northbeammetrics.com>, Tomas Reilly <tomas.reilly@northbeammetrics.com>, Jana Olofsson <jana.olofsson@northbeammetrics.com>
Subject: Re: ROI tool, Friday decision needed
Date: Thu, Apr 30, 2026, 11:55 PM

priya yes rep facing v1 absolutely, every prospect facing tool we have ever shipped became a competitor research project for plumeline within a week, lets not hand them our playbook. also can we please pick ecom for vert one its 60% of my pipeline and b2b saas reps have longer cycles anyway so they can wait a sprint

ps i meant to send that ealrier reply DM to derek not the whole thread, sorry tomas you did not need to be on the receiving end of my excel rant

m

---

From: Tomas Reilly <tomas.reilly@northbeammetrics.com>
To: Marcus Webb <marcus.webb@northbeammetrics.com>
Cc: Priya Shah <priya.shah@northbeammetrics.com>, Heath Banerjee <heath.banerjee@northbeammetrics.com>, Derek Lin <derek.lin@northbeammetrics.com>, Jana Olofsson <jana.olofsson@northbeammetrics.com>
Subject: Re: ROI tool, Friday decision needed
Date: Fri, May 1, 2026, 7:38 AM

Marcus, two things. Write the email, save the draft, send it in the morning. Nothing typed after 11pm has ever improved a thread. "Ealrier" and "asnwer" earlier in this chain. Not a crisis, a pattern.

Agree on ecom first. My realized-median dataset is cleanest there.

Confirming yes on v1 as Priya summarized: rep-facing, ecom vertical first, eight inputs max, range output both ends visible, CRM capture at close, multiplier governance through finance.

Friday decision: approved. Priya, over to you for the spec.

Tomas

---

From: Derek Lin <derek.lin@northbeammetrics.com>
To: Tomas Reilly <tomas.reilly@northbeammetrics.com>, Priya Shah <priya.shah@northbeammetrics.com>, Marcus Webb <marcus.webb@northbeammetrics.com>, Jana Olofsson <jana.olofsson@northbeammetrics.com>, Heath Banerjee <heath.banerjee@northbeammetrics.com>
Subject: Re: ROI tool, Friday decision needed
Date: Fri, May 1, 2026, 9:04 AM

Approved from sales. Ecom v1, rep-facing, range output. Marcus, you are the design partner from the field side, your top three reps test it before anyone else.

Priya, what do you need from me to get the spec moving next week?

Derek
