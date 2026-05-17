# Personal Finance Software Has Lost the Plot

*On the slow drift from "are you going to be okay" to "you spent $14 on coffee"*

May 17, 2026
By Dana Reyes

In 1986, my father sat at the kitchen table with a sheet of green graph paper, a mechanical pencil, and a cheap four-function calculator. He was building a spreadsheet by hand. Each column was a month. Each row was a line item. Mortgage, groceries, the Olds, my piano lessons. At the bottom of every column, in pencil that he sharpened twice an evening, he wrote two numbers. One was what we had. The other was what we needed.

He did this every Sunday for about a decade. I asked him once what he was doing.

He said, "I am checking to see if we are going to be okay."

I have been thinking about that sentence for a year now. Because I have used every major personal finance product released since Quicken 4, and not one of them has answered that question for me.

They have shown me pie charts. They have told me, with what I can only describe as performative concern, that I spent more on restaurants in March than in February. They have nagged me about a Netflix subscription I deliberately keep. They have congratulated me on a "win" that turned out to be a refund I had been chasing for six weeks. What they have not done is the thing my father did with a pencil.

That gap, between what the software is doing and what the software was supposed to be doing, is the subject of this essay.

## A short history of how we got here

Quicken came out in 1983. It was, for its time, miraculous. You could put your checkbook in a computer. The pitch was bookkeeping, not analysis, and that was honest. Quicken did not promise to tell you whether your life was on track. It promised to replace a ledger.

Mint launched in 2007 and changed the frame. The pitch was no longer bookkeeping. It was awareness.

Mint connected to your bank automatically (a genuine breakthrough at the time) and categorized your transactions. The implicit promise was that if you could *see* your money, you could *manage* your money. For a few years this felt like a revolution. Then Intuit bought Mint in 2009, the product stagnated, and the great categorization arms race began.

I want to be fair to the people who built these things. The categorization problem is genuinely hard. Building a system that knows TST*BLUE BOTTLE COF #1142 is a coffee and not a transfer to a small Bahamian bank is real engineering. The mistake was not in solving the problem. The mistake was in believing that solving the problem was the same as building the product.

The mobile wave from roughly 2012 to 2018 produced a generation of apps that looked different but did the same thing. Categorize transactions. Show charts. Maybe send a push notification when you crossed a threshold. Personal Capital pushed upmarket toward wealth dashboards and was eventually swallowed by Empower, where it now lives as a lead funnel for advisors. Mint itself was shut down by Intuit in early 2024, and the survivors (Copilot, Monarch, Lunch Money, Rocket Money, Tiller for the spreadsheet faithful) rushed to mop up the refugees.

I have used all of them. They are, in the technical sense, better than Mint ever was. The sync is more reliable. The categorization is smarter. The visual design is genuinely lovely in places. Monarch's joint-account flow is a small marvel.

And yet.

> "We spent four years convincing ourselves that better categorization was the product. It wasn't. It was the table stakes. The product was supposed to be peace of mind, and we never shipped it."
> Anika Brennan, ex-PM at Mint, on the Off-Books podcast, March 2026

## The job no one is doing

Here is the uncomfortable observation. The replacement products are, almost without exception, expense trackers with extra steps. The home screen is a categorized breakdown of what you spent. The notifications are about spending. The "insights" are about spending. The marketing screenshots, every single one, lead with a donut chart.

This is backward-looking software. It tells you, with admirable precision, what already happened. The implicit theory is that if you see what happened clearly enough, you will change what happens next. This theory is, I think, mostly wrong. Or rather, it is right for about six weeks after onboarding, and then it stops working, which is why retention curves in this category look the way they do.

The job my father was doing on graph paper was not backward-looking. It was forward-looking.

He was not auditing March. He was projecting December. He was asking whether the trajectory, if it continued, landed somewhere he could live with. Backward data was an input. The output was a feeling about the future.

This distinction sounds small. It is not. It is the difference between a tool that makes you anxious about the past and a tool that makes you calmer about what is coming.

> "The category is stuck because the founders all came from fintech or banking. They built tools for people who already think in spreadsheets. Most people don't want a spreadsheet. They want a verdict."
> Jordan Liao, founder of Lunch Money, in an interview with The Plot, February 2026

## Why now

Two things have changed that did not used to be true.

The first is that connecting to bank accounts is, finally, mostly a solved problem. Plaid is not perfect, and the recent MX consolidation has not been clean, but the situation in 2026 is unrecognizable compared to 2015. A small team can ship reliable account aggregation in a quarter. The infrastructure is no longer the moat. This means the moat has to be somewhere else, which is good news for anyone who wants to compete on product instead of on plumbing.

The second is that user expectations have shifted. People have lived through five years of language models that can hold a conversation about their actual situation. They have stopped being impressed by a dashboard. The bar for what counts as "personalized" has moved. A pie chart segmented by Restaurants, Groceries, and Transportation no longer reads as insight. It reads as 2014.

There is also a quieter shift, which is that the audience for this kind of product has aged into a different question. The millennial cohort that adopted Mint in their twenties is now in their late thirties and forties. They are not asking "where did my money go this month." They are asking whether they are going to be able to retire, whether they can afford to have another kid, whether the house thing is ever going to happen.

These are forward-looking, time-bound questions, and the current generation of products does not answer them. It is not even trying.

> "I unsubscribed from Monarch last month. Not because it was bad. Because every time I opened it, it told me about the past, and the past is not what is keeping me up at night."
> Priya Vasquez, writing in her newsletter Cash Rules, April 2026

## What this could look like

I want to be careful here, because I am an essayist and not a founder, and the gap between "someone should build this" and "I have built this" is approximately the size of a career. But it is worth describing, in some detail, what a product built around my father's question might actually feel like.

The home screen is not a dashboard. It is one question and one number, both personalized to you. Something like, "If your current trajectory continues, you will have $612,000 in retirement savings at age 67, which is 71% of the $860,000 you said you wanted." That is the entire home screen. The number updates. The question can change as your life changes. But the shape is always: a forward-looking statement, time-bound, calibrated against something you yourself said you wanted.

Underneath the question is a simulator. Not a chart. A simulator. You can grab the savings-rate slider and pull it from $1,200 a month to $1,500 a month and watch the number on the home screen move from 71% to 78%. You can ask what happens if you defer the kitchen renovation. You can ask what happens if your spouse goes back to work in 2028. The point is not to plan with precision. The point is to develop intuition about which levers in your life actually matter, and which ones (the coffee, always the coffee) do not.

The account data still has to come from somewhere, and it comes from Plaid or whatever has replaced Plaid by the time anyone reads this. The product still has to categorize transactions, because the trajectory math requires it. But categorization is plumbing. It is not the product. It does not get the home screen. It does not get the notifications. It barely gets a tab.

The goals are not generic. You do not pick from a list of "save for a house, save for retirement, build emergency fund." You tell the product, in your own words, what you are trying to do and when.

"I want to retire at 62 with eighty percent of my current income."

"I want to have $90,000 saved for a down payment by the end of 2029."

"I want to be able to pay full in-state tuition for one kid starting in 2034."

The product holds those goals, asks about them periodically, and quietly recalibrates the main number against them. If you said in January that you wanted to retire at 62, and in October your trajectory implies 67, the product tells you that, gently, without a red exclamation point.

There are things this product does not do, and they are as important as the things it does. It does not give investment advice. That way lies regulatory pain and, frankly, hubris. It does not surface a transaction-categorization view as the primary interface, ever, even by accident. It is not trying to be Mint with better fonts. It is trying to be the thing Mint was supposed to be a stepping stone toward, and never was.

The success condition I keep coming back to is not a download number or an ARR target. It is a sentence. If a few thousand people are paying, call it ten or fifteen a month, and a meaningful fraction of them say in an interview, unprompted, "this is the first finance app that hasn't made me feel worse," then the thing is working. That is the bar. Lower than it sounds. Higher than anyone is currently clearing.

## Open questions

I do not know what the pricing model should be. Subscription is the obvious answer and probably the right one, but the category has trained users to expect the core product for free, and the freemium tier in this kind of tool is genuinely hard to design without gutting the value prop. There is also a real question about whether the forecasting model should err conservative (under-promise, over-deliver, possibly depress the user) or expansive (the opposite failure mode, possibly culpable). I lean conservative but I am not sure.

There is also the couples question. Most of the people I know who would buy this product are in a household with someone else, and household finance is not the sum of two individual finances. But building for couples from day one is a different and harder product than building for individuals and adding sharing later. I would build single-user first and feel guilty about it.

These are not blockers. They are the kind of questions you answer by shipping something and watching what happens.

I think someone is going to, and soon. The infrastructure is ready, the audience has aged into the right question, and the incumbents are, charitably, not paying attention.

My father stopped doing the graph paper exercise around 1996, when he finally bought a copy of Quicken. He told me, years later, that he missed the pencil.

He could never quite get the software to answer the question he was actually asking.

Thirty years on, we still haven't. But the gap is now small enough, and the tools are now mature enough, that one good team with a clear thesis could close it. I hope someone does. I would pay for it. I think a lot of people would.

---

*Dana Reyes writes The Plot, a weekly essay on consumer software and the people who use it. Subscribe at theplot.substack.com.*
