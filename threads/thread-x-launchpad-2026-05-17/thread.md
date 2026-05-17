1/30 🧵
most indie launches are dead at hour 3.
i've shipped 4. watched dozens more from friends. the pattern is brutal and almost nobody talks about it honestly.
here's why, and the tool i'm building to fix it. calling it Launchpad.

2/30
last october i launched a tiny saas. 6 weeks prepping. 14 tweets, 3 LinkedIn posts, a Substack drop, a PH listing, an HN Show, and an email to 800 people.
result: 47 upvotes on PH. 9 signups. one paying customer.

3/30
not fishing for sympathy. the math is just insane.
~80 hours of prep. ~$0 in ads. basically nothing to show for it.
QT: @somefounder tweet about "launches are a vibe check on your network not your product"

4/30
he's right and he's wrong.
right: yes, launches measure your network more than your product.
wrong: that doesn't mean the tooling is fine. the tooling is a disaster.

5/30
what actually happened on launch day:
- forgot to post on LinkedIn until 11am
- 6 friends DMed me "wait is it live??"
- missed 3 comment threads on PH because i was answering HN
- by 4pm i was cooked and the launch had 14 hours left

6/30
THIS IS THE PART NOBODY GETS:
a launch is not a marketing event. it's an ops problem.
you are running a 5-day distributed operation across 6 platforms with no dashboard, no queue, no comms layer, and a team of "friends who said they'd help" who are mostly forgetting.

7/30
"just use Buffer" people say.
Buffer is great for sustained content. not built for the launch shape. the launch shape is: massive burst, multi-platform, time-sensitive comment threads, asks-and-favors economy, sentiment-sensitive.
Buffer schedules tweets. that is 5% of the job.

8/30
Product Hunt's algorithm punishes you if you don't get sustained engagement in the first 4 hours. miss it, you fall off the homepage. fall off the homepage, you lose 70% of your traffic.
your first 4 hours need to be a war room. it almost never is.

9/30
X reach on launch posts is. not great. algo de-prioritizes links, your reply guys aren't notified, and your thread might get 800 impressions on a day you needed 80,000.
you can game it but you have to be there, alive, responding, all day.

10/30
the friend-asking-favor tax is real and underrated.
every indie launch involves DMing 15-40 people:
"hey would you mind upvoting?"
"would you mind a comment?"
"did you see it?"
half of them genuinely want to help and forget. you can't follow up without feeling gross.

11/30
so. Launchpad.
[screenshot of a rough Figma mockup: a calendar view with PH, HN, X, LinkedIn, Substack as swim lanes]
one dashboard. one queue. one supporter list. one report card at the end.
built specifically for the 5-7 day window. not a general scheduler.

12/30
a launch is a project. start, end, channels, supporters, outcomes.
right now you run it in 11 browser tabs and a Notes doc. that is insane.
Launchpad is a project management tool for one specific kind of project.

13/30
what it does:
- queues posts across PH, HN, X, LinkedIn, Substack, and your email list
- tracks comment engagement on each platform in one inbox
- nudges supporter friends at the right moment (with permission)
- monitors sentiment
- spits out a report card at the end

14/30
the supporter nudge feature is the one i'm most excited about.
you add yor friends. they opt in. on launch day, Launchpad DMs them at the right moment: "PH post just went live, here's the link, here's a suggested comment if you want one."
no more cringe asking.

15/30
*your
(i need an edit button. i know.)

16/30
feature 1: the queue
draft everything in advance, set the times. Launchpad posts to PH (manual handoff, their API is restricted), drafts the HN Show (also manual, HN has no posting API), auto-posts to X, LinkedIn, Substack, email.

17/30
feature 2: the inbox
every comment on every platform in one feed. PH, HN, X, LinkedIn, Substack. sorted by recency or "needs response."
you respond from inside Launchpad. it posts back to the platform.

18/30
feature 3: the supporter board
your friends, opted in.
their preferred platforms.
their preferred nudge time.
Launchpad DMs them (X) or emails them at the optimal moment with a one-click "i engaged" check-in. you see who showed up.

19/30
feature 4: sentiment monitor
scans the comment feed for tone. flags hostile replies fast so you can respond before they fester. flags enthusiastic replies fast so you can amplify them.
not magic. just an LLM pass on the inbox. but it changes the day.

20/30
feature 5: the report card
day 8, you get a doc.
what worked. what didn't. which channel drove signups. which supporters showed up. what to do differently next time.
most launches generate zero institutional knowledge. this fixes that.

21/30
ok. open questions. things i have not figured out.

22/30
ok i oversold the supporter-nudge thing in tweet 14. nuance: it only works if friends actually opt in, and getting 15 people to opt in to yet another tool is real friction. probably solved with a no-account magic-link flow but it's unproven.

23/30
pricing. how do you price a tool people use 1-3 times a year?
options:
- one-time per launch ($99? $199?)
- annual subscription ($29/mo) and you eat the dead months
- credits / pay-per-launch with a lifetime deal upsell
leaning per-launch. not sure.

24/30
is this venture-scale? probably not. is it a $1-3M/yr indie SaaS run by 2 people? i think so. being honest: maybe 50-100k serious indie launchers globally. tam is real but it's not Notion.

25/30
who i'm looking for:
design partners. indie founders launching something in the next 60 days who'd let me shadow the launch and use a v0 of Launchpad. i do half the ops with you. you get a free year. i get real data.

26/30
also looking for:
- someone who knows PH's review process (they reject 30%+ and i want a checklist)
- someone who's been a top HN poster and knows the patterns
- someone who can sanity check my LinkedIn algorithm assumptions

27/30
what this is NOT:
not a Buffer/Hootsuite competitor. those are for sustained content. this is launch-week-only.
not a launch coach. tool only, no advice for sale.
not an audience-buying marketplace. not connecting you to paid upvoters. that's gross and against TOS.

28/30
why post this publicly instead of building in stealth?
because building in stealth on a tool for launches is a contradiction. the audience IS twitter indie hackers. you are the user. if i can't convince you in a thread, the product won't work.

29/30
if this resonates, like + bookmark and i'll DM the deck and a waitlist link.
(actually no DM theater. waitlist is at launchpad.dev/waitlist. deck link in there. closing it at 500 to keep the design-partner cohort tight.)

30/30
appreciate you reading all 30.
if you've launched something in the last year, reply with one thing that broke for you. reading every reply and the patterns go straight into the v1 spec.
let's make indie launches less painful.

—

self-reply: forgot to mention: integrations roadmap is PH + X first, then LinkedIn + Substack, then HN draft helper, then email connectors (ConvertKit, Beehiiv, Substack-as-email, Mailchimp). email is the unsexy one and probably the most important. your list is your only owned audience. everything else is rented.

self-reply: if your launch tooling can't tell you which supporters actually showed up, how do you know who to thank, who to reciprocate for, and who to quietly stop asking?
