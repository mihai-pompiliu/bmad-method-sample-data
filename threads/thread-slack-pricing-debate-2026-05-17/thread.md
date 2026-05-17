#pricing-strategy

*Dana Okafor* 9:02 AM
Ok team, pulling the trigger on the conversation we keep half-having. I want a real decision (or at least the shape of one) before board prep on the 28th. Question: do we move Lumengrid off seat-based pricing and onto consumption-based.

*Dana Okafor* 9:03 AM
Context: seat pricing is capping expansion. Customers buy 20 seats, 6 people actually use it, they stall. Meanwhile they ingest 4x the volume of logs we modeled for.

*Marco Reyes* 9:05 AM
Strong agree from growth. Top quartile accounts are leaving money on the table because the meter doesn't match the value. They get more value every quarter and pay us the same amount. That's a leak.

(reactions: plus_one x3, eyes x2)

*Priya Shah* 9:06 AM
Flagging the engineering side before anyone gets too excited. Consumption pricing means metering has to be bulletproof. Right now our metering pipeline drops events under load. 0.4% drop rate last week. Fine for dashboards. Not fine for billing.

*Marco Reyes* 9:07 AM
Fair. But that's a fixable problem, not a strategic one.

*Priya Shah* 9:08 AM
Fixable on a timeline. I'd want a full quarter of metering hardening before we put a dollar sign on those numbers. Otherwise we get an angry CFO email we can't defend.

*Wes Trent* 9:10 AM
CSM hat on. My top 10 will revolt. Six of them specifically chose us over Datadog because we're predictable. Flat seat price, they know what they owe. If we move to consumption they'll feel ambushed. And some will use it as the excuse they've been waiting for to renegotiate the whole contract.

(reactions: hundred x1, grimacing x2)

*Dana Okafor* 9:12 AM
That is exactly the concern I want to put on the table. Wes, of those 10, how many are coming up for renewal in the next two quarters?

*Wes Trent* 9:13 AM
Four. Northbeam, Halverson Logistics, Crestline, and the unnamed one we don't talk about in writing.

*Dana Okafor* 9:13 AM
Got it. So whatever we decide, those four are grandfathered. Non-negotiable.

*Marco Reyes* 9:14 AM
Agreed on grandfathering. That's standard. But we shouldn't let the top 10 dictate the model for the next 500 customers.

*Wes Trent* 9:15 AM
I hear you but losing even two of those four is a 9 percent revenue hit. That's not a rounding error.

*Priya Shah* 9:16 AM
Also worth naming: the top 10 are also our biggest log volume customers. Under consumption pricing they would pay MORE, not less. So grandfathering them is actually leaving the highest-yield accounts on the old model. The math gets weird fast.

(reactions: thinking_face x4)

*Lin Castellanos* 9:18 AM
Designer lurking. Dumb question: what does the pricing page even look like under consumption? Right now it's three tiers and a "talk to sales" box. Consumption pricing pages are notoriously hard to communicate. Per-GB? Per-event? Per-trace? Each reads very differently to a buyer.

*Marco Reyes* 9:20 AM
Good question, not dumb. I'd lean per-GB ingested with a free tier and overage. It's the model buyers already understand from S3 and Datadog.

*Priya Shah* 9:21 AM
"The model buyers already understand from Datadog" is also the model buyers already RESENT from Datadog.

(reactions: laughing x5, fire x2)

*Marco Reyes* 9:22 AM
Lol fair point.

*Dana Okafor* 9:23 AM
Something from the field. I was on a call yesterday with the VP Eng at Mirabel Health (~$40k ARR). She told me unprompted that they're spinning down half their Lumengrid usage next month because they're doing a cost review and can't justify the seats they have. Then ten minutes later she's bragging that they ingest 2 TB a week through us and it's their most-loved internal tool. Same call. They love us. They are also cutting us. Because the bill doesn't match the love.

(reactions: heart x3, exploding_head x2)

*Wes Trent* 9:25 AM
Ok that one stings. That's a clean case for consumption.

*Marco Reyes* 9:26 AM
That's the whole argument right there. The product is loved. The pricing is fighting us.

*Priya Shah* 9:27 AM
I don't disagree with the diagnosis. I am asking us not to ship the cure before the syringe is sterile.

(reactions: plus_one x4)

*Dana Okafor* 9:28 AM
Priya what's the realistic timeline for the metering pipeline to be billing-grade?

*Priya Shah* 9:29 AM
P0 with two engineers off the agent rewrite: 10-12 weeks. Current staffing: 18-20 weeks. And pulling people off the agent rewrite has its own cost. We promised the Crestline team that ships in Q3.

*Wes Trent* 9:31 AM
Crestline is one of my four renewals. Do not slip Crestline.

*Marco Reyes* 9:32 AM
So we're saying we can't move pricing without slipping a top-10 commitment, which is the exact same top-10 we're worried about churning. That's a real bind.

(reactions: hundred x2, grimacing x1)

*Lin Castellanos* 9:34 AM
What if we A/B test it? Run consumption pricing on new signups only for 60 days, leave everyone else alone, see what happens to conversion and ARR. Lower risk way to learn.

*Marco Reyes* 9:35 AM
I love the instinct but pricing isn't really A/B testable. Word gets out fast. Two prospects compare notes in a Slack community and now we have a credibility problem.

*Dana Okafor* 9:35 AM
Yeah we tried something like this in 2024 at my last company. Within 11 days a customer posted screenshots of two different quotes side by side on LinkedIn. It was a whole thing.

*Lin Castellanos* 9:36 AM
Ah. Withdrawn.

(reactions: salute x3)

*Wes Trent* 9:37 AM
Also from a CSM standpoint, I would have to lie to prospects about why their friend at another company got a different price. Hard pass.

*Marco Reyes* 9:38 AM
So A/B is out. What about a one-way door: announce new pricing for new customers as of date X, existing customers can stay on legacy pricing for 12 months. Transparent, not split-tested.

*Dana Okafor* 9:39 AM
That I can defend. That's not a test, that's a transition.

*Priya Shah* 9:40 AM
Still depends on the metering pipeline being ready by date X. Don't announce a date I can't hit.

*Dana Okafor* 9:40 AM
Understood.

*Sam Liu* 9:43 AM
Sorry late, was in a finance review. One number: under the consumption model Marco mocked up last week, gross margin drops from 78% to roughly 64% in year one because of egress and storage cost variance. We claw it back in year two but the board is going to ask about the dip. Also consumption revenue is harder to forecast. Board likes seat-based because they can multiply two numbers together.

(reactions: chart_with_downwards_trend x2, eyes x3)

*Marco Reyes* 9:45 AM
14 points of margin is a real hit. I had it modeled at 9.

*Sam Liu* 9:46 AM
You modeled it before the egress contract reprice in March. New egress numbers are uglier.

*Marco Reyes* 9:46 AM
Ugh. Ok.

*Dana Okafor* 9:47 AM
Sam can you send me the updated model after this thread? I want to walk through it before I talk to Janine.

*Sam Liu* 9:47 AM
Yep, will drop it in your DMs by EOD.

*Wes Trent* 9:50 AM
Unrelated, where is everyone getting lunch. The thai place has had a "back tomorrow" sign for three weeks.

*Lin Castellanos* 9:51 AM
Poke bowl place on 4th is good. Skip the spicy mayo, it's just sriracha.

*Marco Reyes* 9:52 AM
New tacos truck on Folsom and 2nd. Carnitas was great yesterday.

*Wes Trent* 9:52 AM
Sold. Tacos it is.

(reactions: taco x6, poke x1)

*Dana Okafor* 9:53 AM
Ok refocusing. Where I think we are:

*Dana Okafor* 9:54 AM
1. Consumption pricing solves a real problem (love/bill mismatch, Mirabel story)
2. We can't execute it cleanly until metering is billing-grade (10-20 weeks)
3. Top 10 accounts need grandfathering, but grandfathering them inverts the economics (Priya's point)
4. A/B test is out, one-way transition for new customers is plausible
5. Margin hit is bigger than we modeled (Sam, 14 points not 9)
6. Board will want a predictability story regardless

*Dana Okafor* 9:55 AM
Things I don't yet have a position on: whether the right move is a hybrid (base platform fee + consumption overage) instead of pure consumption. That might soften the margin dip and the forecasting issue at the same time.

*Marco Reyes* 9:56 AM
Hybrid is what Datadog actually does. And Honeycomb. And basically every observability product that's still alive.

*Priya Shah* 9:57 AM
Hybrid has the same metering requirement. The pipeline still has to be billing-grade.

*Wes Trent* 9:57 AM
But hybrid is way easier to sell to my book. "Your seat price doesn't change, you just pay for what you ingest above X" is a much smaller pill.

*Lin Castellanos* 9:58 AM
And honestly easier to put on a pricing page. Base tier with a meter underneath is a pattern people already get.

*Sam Liu* 9:59 AM
Hybrid forecasts better too. The base fee gives us the floor for the board math, the consumption gives us the upside story.

(reactions: plus_one x5, lightbulb x3)

*Dana Okafor* 10:00 AM
Ok this is interesting. Feels like the heat is on pure consumption vs hybrid, not seat vs consumption.

*Marco Reyes* 10:01 AM
I'd push for pure consumption long term and hybrid as the transition vehicle. Get them on the meter, then over time the base fee becomes less of the bill.

*Wes Trent* 10:02 AM
That is exactly what customers will sniff out and resent though. "You said it was a base fee and now four years later we're paying you like Datadog."

*Marco Reyes* 10:02 AM
Then we don't say that out loud. We just price it that way.

*Priya Shah* 10:03 AM
That's the kind of thing that gets read out at a board meeting in two years and ruins someone's week.

(reactions: skull x2, popcorn x3)

*Dana Okafor* 10:04 AM
Yeah I don't love the trojan horse framing Marco. Either we believe in the hybrid as the actual model or we don't.

*Marco Reyes* 10:05 AM
Fair pushback. I'll back off the framing. I still think directionally the meter should grow as a share of revenue over time, but not as a hidden agenda.

*Wes Trent* 10:06 AM
That I can live with.

*Sam Liu* 10:08 AM
One more thing to flag before anyone leaves: if we're going hybrid, the contract template needs a rewrite. Legal is already backed up on the SOC2 stuff. They'll need 4-6 weeks just for the paperwork.

*Dana Okafor* 10:09 AM
Add it to the dependency list. Priya can you and Sam draft a one-pager on the metering + legal + finance dependencies by Wednesday? I don't need a plan, just a list of what has to be true before we can announce anything.

*Priya Shah* 10:10 AM
Yep, will do.

*Sam Liu* 10:10 AM
On it.

*Wes Trent* 10:12 AM
For the CSM side I'd like to do a quiet listening tour with the top 10 before any announcement. Frame it as "we're thinking about pricing changes, want your input." Gets us ahead of the surprise factor.

*Dana Okafor* 10:13 AM
Yes please. But coordinate with Marco so the message is consistent.

*Marco Reyes* 10:13 AM
Will do. Wes let's grab 20 minutes tomorrow.

*Wes Trent* 10:14 AM
Done, sending invite.

*Lin Castellanos* 10:16 AM
Design question parked but flagging: whenever we do land on a model, the pricing page is probably a 3-4 week project on my side. Not blocking but I want it in the timeline so it doesn't become a fire drill.

*Dana Okafor* 10:17 AM
Noted. Add it to the dependency one-pager.

*Marco Reyes* 10:19 AM
One thing I want to name before we wrap: we've been having this conversation in some form for about seven months. I'd like us to actually decide something this quarter, even if the decision is "not yet."

*Dana Okafor* 10:20 AM
Agreed. And I hear that. I don't want to force a decision today though, I want the dependency picture from Priya and Sam first.

*Wes Trent* 10:21 AM
Same. Decision under uncertainty is fine. Decision under self-inflicted ignorance is not.

(reactions: hundred x4)

*Priya Shah* 10:22 AM
That's going on a t-shirt.

*Dana Okafor* 10:24 AM
Ok I have to jump to the 10:30. Let's pick this up Monday. Priya/Sam one-pager by Wednesday, Wes/Marco coordinate on the listening tour, Lin keep the pricing page redesign warm but don't start. We'll regroup Monday 9 AM and try to land on hybrid vs pure consumption as the working hypothesis.

*Dana Okafor* 10:24 AM
Thanks all. Good thread.

(reactions: plus_one x6, salute x2, taco x1)
