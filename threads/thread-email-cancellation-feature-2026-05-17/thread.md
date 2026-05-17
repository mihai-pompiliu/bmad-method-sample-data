From: Priya Natarajan <priya.natarajan@lumenmetric.io>
To: Daniel Roe <daniel.roe@lumenmetric.io>
Date: Mon, May 17 2026, 8:42 AM PT
Subject: Re: Re: Re: Re: Re: Re: Northwind cancellation - need a call today

ok let's do option B.

-d


From: Daniel Roe <daniel.roe@lumenmetric.io>
To: Priya Natarajan <priya.natarajan@lumenmetric.io>
Cc: Marcus Field <marcus.field@lumenmetric.io>, Sasha Bellamy <sasha.bellamy@lumenmetric.io>, Tomas Renko <tomas.renko@lumenmetric.io>
Date: Mon, May 17 2026, 7:55 AM PT
Subject: Re: Re: Re: Re: Re: Northwind cancellation - need a call today

Trying to land this plane before the 10am exec sync. Here is where I think we are.

The customer (Northwind Foods, $96k ARR, 14-month tenure, renewal was due in 38 days) is leaving. Confirmed by Sasha this morning. They are not asking us to save them. They have already signed with Quartile. So the question is not "how do we keep Northwind" because that ship is gone. The question is "what do we do so the next Northwind does not leave the same way."

We have three theories on the table. Let me try to write them down honestly so we can pick one and stop arguing in this thread.

Theory 1 (Sasha / CS): The product is missing a query result caching layer for warehouse-connected dashboards. Northwind's analysts were waiting 40-90 seconds per dashboard load against their Snowflake. Quartile caches aggressively and feels instant. Build the cache, win the segment.

Theory 2 (Tomas / Eng): The product is not missing anything. Northwind had a misconfigured Snowflake warehouse (XS, auto-suspend at 60s) and was running dashboards that scanned 400M+ rows on every load with no materialized views. We sent them a tuning guide in February. They did not act on it. A cache layer papers over a customer-side problem and will balloon our infra costs to subsidize bad warehouse hygiene. The real gap is that we do not surface query cost or scan size to the end user, so analysts had no idea what they were doing was expensive.

Theory 3 (me / Product): Both of the above are partially true but neither would have saved this account on its own. The real gap is that Northwind had no idea the dashboards were slow until their CFO complained in a board meeting. We have no in-product signal to the admin that "your users are waiting 60s+ for dashboards N times a day." Quartile sends a weekly performance digest. We do not. By the time CS heard about it, the CFO had already taken three demos.

Reading the chain back, I think Marcus's instinct (just build the cache, ship it in 6 weeks, market it) is the fastest thing to communicate externally and the easiest to put on a roadmap slide. Tomas is correct that it does not fix the underlying issue and will cost us margin. Sasha is correct that customers do not care about the underlying issue, they care about wait time.

Proposed path forward, pick one:

Option A: Build the warehouse query cache. 6-8 weeks eng, ~$40k/mo additional infra at current scale, marketing moment, defensible against Quartile on the perf axis. Does not fix observability gap. Tomas will not love it but will build it.

Option B: Ship the performance digest + in-app slow-query banner first (3 weeks), then revisit caching in Q3 once we have data on which customers are actually hitting the wall vs which are misconfigured. Slower external story, better long-term product.

Option C: Both, sequenced. Digest in 3 weeks, cache starts in parallel, ships end of Q2. ~$60k all-in cost. The "have it all" answer. Probably what we actually do but I want to hear from you before we commit eng to it.

One thing I want to flag that none of the three options actually solves. The deepest thing Anita said in her note was not about speed. It was that she paid us six figures to not have to know what a clustering key is, and we silently required her to know it anyway. None of A, B, or C changes that. A masks it with a cache. B exposes it to the analyst who already does not act on it. C does both of those things. None of them changes the fact that the person who signs the check has no idea what the product needs from her org until something breaks in front of her CFO. I do not have a clean roadmap answer for that and I am not going to pretend I do in the next 4 hours. But I want it on the record that we are picking among three options that do not address the actual thing the customer told us.

I am leaning B. Daniel, your call. Need to know by EOD because Tomas's team is mid-sprint planning and Marcus wants to put something in the QBR deck Wednesday.

Priya

> From: Tomas Renko <tomas.renko@lumenmetric.io>
> To: Daniel Roe <daniel.roe@lumenmetric.io>
> Cc: Priya Natarajan, Marcus Field, Sasha Bellamy
> Date: Mon, May 17 2026, 6:18 AM PT
> Subject: Re: Re: Re: Re: Northwind cancellation - need a call today
>
> I want to push back hard on this before it becomes a roadmap item by inertia.
>
> We are about to commit a quarter of engineering capacity to building a caching layer because one account we already lost said they wanted faster dashboards. Let me lay out what is actually going on under the hood, because I do not think anyone outside eng has looked at Northwind's query logs.
>
> Their slowest dashboard, the "Regional Sales Daily" one Sasha mentioned, runs a query that scans 412 million rows in their orders_fact table on every load. They have no clustering key on the table. They are using a Snowflake XS warehouse with auto-suspend at 60 seconds, which means every dashboard load is a cold start. They have 11 widgets on that dashboard, each running an independent query, none of them sharing a CTE. Their analyst team built it that way 9 months ago and has not touched it since.
>
> We told them this. I personally sat on a call with their data lead in February and walked through it. We sent a written tuning recommendation. They thanked us and did nothing.
>
> Now, you can argue (and Sasha is arguing) that "they should not have to do that, it should just be fast." Fine. But understand what we are signing up for if we cache results:
>
> 1. Cache invalidation on warehouse-connected data is genuinely hard. We will get this wrong at least twice in the first 6 months. Customers will see stale data and not know it.
> 2. The infrastructure cost is non-trivial. Rough math: at our current dashboard load volume, materializing and storing query results for a 24-hour TTL across all customers is between $35k and $50k per month additional spend on object storage and the cache cluster.
> 3. We are masking the actual problem, which is that our customers do not know their own warehouses are misconfigured. We become a band-aid for bad data engineering. The next Northwind will hit a different wall (concurrency limits, MERGE costs, whatever) and we will be on the hook to paper over that one too.
>
> Quartile caches because Quartile's customers are smaller and have simpler warehouses. We sell to mid-market with real data volume. The right answer for our segment is not to hide cost from the user, it is to expose it.
>
> What I want to build instead: query cost telemetry surfaced in the dashboard editor. "This widget scans 412M rows and takes 47 seconds. Here are 3 suggested optimizations." We can ship a v1 of that in 4 weeks. It actually teaches our customers to be better at their own jobs and it differentiates us from Quartile on a real axis instead of a vanity one.
>
> I will also note, since nobody else has, that Quartile is going to hit a wall with Northwind around month 4. Their caching strategy assumes a working set that fits in memory on a single node. Northwind's analyst team runs 60+ unique dashboards a week against a fact table that grows 8M rows a month. The cache hit rate is going to collapse as soon as their usage spreads out, and they are going to be right back to slow queries against the warehouse, except now they will also have a stale-data problem when the cache lies to them. We could win this account back in 12 months if we hold our nerve and build the right thing.
>
> I understand we lost a $96k account. I understand it stings. I do not think the answer is to capitulate to the loudest piece of feedback from a customer who, with respect, was not using the product the way it was designed to be used. We are 80 people. We do not have the engineering capacity to be all things to all segments. We have to pick.
>
> Tomas
>
>> From: Daniel Roe <daniel.roe@lumenmetric.io>
>> To: Priya Natarajan, Marcus Field, Sasha Bellamy, Tomas Renko
>> Date: Sun, May 16 2026, 9:47 PM PT
>> Subject: Re: Re: Re: Northwind cancellation - need a call today
>>
>> Looping Tomas in because I want eng's read on this before we commit to anything in the QBR.
>>
>> Marcus, I hear you on the urgency. I am not against building the feature. But I want to make sure we are reading the signal correctly. We have lost two accounts in the last six months citing "performance" in their churn note. Northwind is the bigger one, the other was Coastal Logistics at $34k. In both cases the actual complaint when CS dug in was dashboard load time on warehouse-connected views with large fact tables.
>>
>> Three things bug me about jumping straight to "build the cache":
>>
>> First, I do not actually know if a query cache would have saved Northwind. They told Sasha they were leaving on April 30. Their renewal was July 1. By the time we shipped a cache, they would have been on Quartile for 2 months. We are building for the next account, not this one, and we should be honest about that.
>>
>> Second, Sasha mentioned their CFO got involved because dashboards were "slow during the board prep window." That is a usage pattern. They were not slow at 2pm on a Tuesday. They were slow when 8 people opened the same dashboard within 10 minutes of each other on a Sunday night. That is a concurrency story, not a single-query latency story. A cache might help. So might warehouse sizing guidance. So might a heads-up to the admin that "your team is about to hammer this dashboard."
>>
>> Third, we have a known issue list of 14 perf-related items in the backlog right now. Some are 1-week fixes. The cache is a quarter of work minimum. Are we sure that is the right first move, or are we picking the most ambitious option because it sounds like the most decisive response to losing an account?
>>
>> I want to talk about this Monday morning before the exec sync. Priya, can you put a real proposal together overnight with options? Not a recommendation, options. I want to see the tradeoffs side by side.
>>
>> One more thing. I read Anita's note three times. The part that is sticking with me is not the slowness. It is "I never knew the dashboards were slow until it was too late." That is the part I cannot stop thinking about. We built a product where the admin who signs the check has no visibility into whether the product is actually working for her team. That is a deeper problem than query latency and I do not see it on anyone's roadmap. I want that in the proposal too.
>>
>> -d
>>
>>> From: Marcus Field <marcus.field@lumenmetric.io>
>>> To: Priya Natarajan, Daniel Roe
>>> Cc: Sasha Bellamy
>>> Date: Sun, May 16 2026, 6:22 PM PT
>>> Subject: Re: Re: Northwind cancellation - need a call today
>>>
>>> Daniel, you should see this. Top of inbox Monday.
>>>
>>> Priya, I have been saying for two quarters that we need a caching layer on warehouse-connected dashboards. Every single competitive loss I have reviewed in the last 9 months has had "slow" or "performance" somewhere in the disqualification note. Quartile leads with it in their pitch. Their demo opens with a 9-widget dashboard loading in under 2 seconds against a Snowflake connection. We cannot compete on that in a live bake-off and we have not been able to since I joined.
>>>
>>> I do not want to hear about query cost telemetry or "teaching the customer." Customers do not want to be taught. They want it to be fast. That is the product. Snowflake is expensive, BI tools are supposed to make Snowflake feel less expensive, that is the entire category.
>>>
>>> Northwind is not a one-off. We have at least 4 more accounts in the renewal window in Q3 that have raised performance flags in their QBRs. Brightline ($112k), Hartwell Industries ($78k), Coastal Maritime ($88k), and Drury Brands ($61k). If we lose two more like Northwind we are looking at a $300k+ ARR hole this fiscal year. That is real money against the Series B narrative.
>>>
>>> I will also remind everyone that we positioned ourselves to the board last cycle as "the BI platform for mid-market data teams." Mid-market data teams have messy warehouses. That is the segment. If we are going to require customers to have a tuned Snowflake before our product works, we are not actually serving that segment, we are serving the segment above it, and we should say that out loud and reprice accordingly.
>>>
>>> Build the cache. Six weeks. Put it on the roadmap. Market it hard. Stop overthinking it.
>>>
>>> Marcus
>>>
>>>> From: Priya Natarajan <priya.natarajan@lumenmetric.io>
>>>> To: Marcus Field <marcus.field@lumenmetric.io>
>>>> Cc: Sasha Bellamy <sasha.bellamy@lumenmetric.io>
>>>> Date: Sun, May 16 2026, 4:11 PM PT
>>>> Subject: Re: Northwind cancellation - need a call today
>>>>
>>>> Marcus, thanks for the forward. Sasha, sorry, I know this one has been a grind for months.
>>>>
>>>> Reading Anita's note carefully. A few things jump out.
>>>>
>>>> She does not say "your product is missing X feature." She says the dashboards are slow, her team stopped using them, and she lost the room with her CFO. That is a usage and trust failure as much as a feature failure. By the time the CFO is involved, the procurement decision is already 80% made on emotional grounds.
>>>>
>>>> She mentions Quartile by name and says the demo "felt instant." I would bet a non-trivial amount that what they demoed her was a pre-warmed cached dashboard against a small sample dataset. That is a real capability gap but it is also a demo trick. We need to figure out which one is doing the work in her head.
>>>>
>>>> The dashboards she calls out, "Regional Sales Daily" and "Inventory Turn by SKU," are both warehouse-connected against their Snowflake. I want to pull the query logs on those before I form an opinion. Sasha, can you get Tomas's team to pull last 30 days of query times for the Northwind workspace?
>>>>
>>>> I do not want to commit to a roadmap response in this thread. I want to understand what actually happened before we decide what it tells us about the product. Loud feedback from the loudest churn is not always the right signal to optimize on. Sometimes it is. Sometimes the customer is telling us a story about themselves and we are reading it as a story about us.
>>>>
>>>> One thing that does jump out as a real gap regardless of what the query logs show: Anita literally writes "I never knew the dashboards were slow until it was too late." That is on us. We have admin-side telemetry for licenses and seat usage but nothing for end-user experience. Whatever else we decide, that gap is real and it is not in dispute.
>>>>
>>>> Will have a read by Monday EOD.
>>>>
>>>> Priya
>>>>
>>>>> From: Marcus Field <marcus.field@lumenmetric.io>
>>>>> To: Priya Natarajan <priya.natarajan@lumenmetric.io>
>>>>> Cc: Sasha Bellamy <sasha.bellamy@lumenmetric.io>
>>>>> Date: Sun, May 16 2026, 2:48 PM PT
>>>>> Subject: Fwd: Northwind cancellation - need a call today
>>>>>
>>>>> Priya,
>>>>>
>>>>> See below. Anita Voss at Northwind sent this to Sasha Friday afternoon. Sasha and I have been trying to save this account since February, you know the history. They are leaving for Quartile. $96k ARR walking out the door.
>>>>>
>>>>> The reason is the one we have been talking about for months and the one I have been raising in roadmap reviews since January. Dashboard performance on warehouse-connected data. Anita is unusually candid about what happened internally on her side, please read the whole thing.
>>>>>
>>>>> I need to know what we are doing about this. Not "we are looking into it." A real answer. I have three more renewals in Q3 with similar exposure and I cannot sit across from those CSMs and tell them we are still "evaluating" whether dashboard speed is a priority.
>>>>>
>>>>> Can you reply today.
>>>>>
>>>>> Marcus
>>>>>
>>>>>> From: Anita Voss <anita.voss@northwindfoods.com>
>>>>>> To: Sasha Bellamy <sasha.bellamy@lumenmetric.io>
>>>>>> Date: Fri, May 14 2026, 4:32 PM ET
>>>>>> Subject: Northwind cancellation - need a call today
>>>>>>
>>>>>> Sasha,
>>>>>>
>>>>>> I appreciate the partnership over the last year and I want to be straight with you because I know you will ask, and I would rather you hear it from me than from procurement.
>>>>>>
>>>>>> We are not renewing Lumenmetric. We signed with Quartile on Wednesday. Implementation starts June 1. I wanted to give you the heads-up before the formal notice goes through your legal channel next week.
>>>>>>
>>>>>> This is not about pricing and it is not about your team. You have been responsive, the QBRs have been useful, and your implementation last spring was the smoothest vendor onboarding we have done in three years. I want to be clear that the relationship side of this has been good.
>>>>>>
>>>>>> The reason is that the product became unusable for my analyst team over the last six months. I am going to walk you through what happened because I think you should know, and because I owe you the honesty.
>>>>>>
>>>>>> When we onboarded last April, we built out about 30 dashboards on top of our Snowflake warehouse. Most of them were fine. The two that mattered most to the business, Regional Sales Daily and Inventory Turn by SKU, started slowing down around October as our data volume grew. By January, my team was waiting 40 to 90 seconds for those dashboards to load. By March, they had stopped using them and were running ad-hoc queries in the Snowflake console instead.
>>>>>>
>>>>>> I did not know any of this. My VP of Analytics did not flag it because his team had quietly worked around it. I found out the hard way. We were prepping for the April board meeting, my CFO opened Regional Sales Daily live in a pre-read session, and it spun for 73 seconds before loading half the widgets. He asked me, in front of two board members, why we were paying six figures for software that was slower than Excel. I did not have a good answer.
>>>>>>
>>>>>> That is the moment I started looking at alternatives. By the time your team reached out about renewal in early May, I had already seen three demos. Quartile's demo loaded a comparable dashboard against a comparable warehouse in under three seconds. I do not know if that holds up in production. I am willing to find out.
>>>>>>
>>>>>> I want to flag two things for your product team, not as a complaint, just as information.
>>>>>>
>>>>>> One. I never knew the dashboards were slow until it was too late. There was no alert, no email, nothing in the admin console that said "your users are waiting a long time for this." If there had been, I would have called you in November and we would have fixed it together. By April I was already emotionally checked out.
>>>>>>
>>>>>> Two. I am told by my data lead that some of this might have been our warehouse configuration. I do not really care. We pay you so that I do not have to know what a clustering key is. If the product needs me to tune Snowflake to make it work, that should be in the sales conversation, and it should be much louder than it currently is.
>>>>>>
>>>>>> I am sorry to do this over email. I am happy to do a closeout call next week if you want one. I will give you a clean reference for the parts of the product and the team that worked well, and you can ask me anything you want about the rest.
>>>>>>
>>>>>> If it is useful to your product team, my data lead said he is willing to share the actual query logs and dashboard configs with your engineering org under NDA. He thinks there is a real story in there for you, and he liked your team enough to want to help even though we are leaving. Just let me know.
>>>>>>
>>>>>> Take care of yourself.
>>>>>>
>>>>>> Anita Voss
>>>>>> VP, Finance and Strategy
>>>>>> Northwind Foods
