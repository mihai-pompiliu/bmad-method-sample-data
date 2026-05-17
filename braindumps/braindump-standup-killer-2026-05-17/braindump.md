## okay I'm just going to type

It's 11:14pm. I sat in three standups today. Three. One was 22 minutes.
One was 31 minutes (THIRTY ONE, for a team of nine, do the math, that's
almost five engineer-hours of meeting for what amounted to "yeah still
working on the auth thing"). The third one I dialed into from the car
because I was picking up groceries and I genuinely cannot tell you what
was said in it. And I sat there in the parking lot with a bag of frozen
peas melting on the passenger seat and I thought: I have been thinking
about killing this meeting for a YEAR. A whole year of "someone should
build this" and tonight I'm just going to write down what it is and
start tomorrow because I have $400K of runway and if I burn another
quarter "thinking" I deserve what happens to me. So. Here we go.

The thesis is simple and I want to say it before I get clever about it:
*the daily standup meeting should not exist*. Not "should be shorter."
Not "should be better facilitated." Should not EXIST. Every engineer
hates standups (and yeah, I know, I know, I can hear someone in my head
saying "well actually some teams really value the ritual" and fine,
sure, but let's be honest, 90% of standups are theater that exist
because a manager four years ago read a book about Scrum and now we're
all paying tax on it forever). The information that's exchanged in a
standup is *already in the system*. It's in Linear. It's in Jira (god
help you). It's in GitHub. It's in the Slack channel where the actual
blockers get aired at 4pm because no one wanted to raise them at 9:30am
in front of the VP. It's in the calendar that shows you Sarah is in
interviews all day Thursday. The data is THERE. Standup is just humans
badly summarizing data that a model could summarize better, faster, and
without making me sit in a Zoom with my camera off pretending to pay
attention.

So here's what I want to build. An AI-native async standup tool that
pulls from Linear/Jira/GitHub/Slack/calendar (we'll start with Linear +
GitHub + Slack, Jira is hell but we'll get there, calendar is table
stakes), and every morning at like 8am local time it produces a
3-sentence summary per team member of what they actually did yesterday
and what they're probably working on today. Not "I'm continuing to work
on the auth refactor" for the eighteenth day in a row. Actual signal:
"Shipped 3 PRs to the billing service, including the long-running
webhook fix. Spent ~4 hours in the auth refactor branch but no commits
since Tuesday, likely stuck. Has a 2hr block on calendar this morning
labeled 'deep work, do not disturb'." Three sentences. Per person. And
then the team lead gets ONE digest. One. A single digest they can read
in under a minute (I keep going back and forth on whether the target is
60 seconds or 90, let's say 60, if a lead can't grok their team's state
in 60 seconds we haven't earned the right to replace the meeting). And
critically: nobody types into a bot. Nobody fills out a form. The whole
point is that the *humans don't have to do anything*. Geekbot makes you
type your update. Status Hero makes you type your update. We do not
make you type your update. That's the whole game. If a human has to log
in and write something, we have failed and we are just a worse meeting.

The piece I keep coming back to, the piece I think is actually the
wedge, is the blocker inference. Look, anyone can summarize git
activity, that's a weekend project, that's not a company. The thing
that makes this a *product* is that the model has to look at the data
and say "Brian has had the same PR open for 3 days, his last commit was
Monday, he's been pasting stack traces in #eng-help, he's blocked,
surface this to the lead." That's the magic. That's the thing standup
is theoretically supposed to do (and never does, because people don't
want to say "I'm stuck" in front of seven peers, they just don't, I've
been on both sides of that table and it doesn't happen). Linear does
pattern detection on cycle time. Linear does this WELL actually, I'll
give them that, their insights stuff is genuinely good. We can't be
worse than Linear at noticing when someone is stuck. We have to be
BETTER, because we have Slack and calendar context that Linear doesn't
have. If we can't beat Linear at this we don't have a business. Maybe
that's wrong. Actually no, I think I'm right. But I should
pressure-test it with five eng managers before I bet the company on it.

A few things this is NOT and I want to write them down so I stop
drifting. This is not a project management tool. I am not building
Linear. I am not building Asana. I am not building Jira (lord). We READ
from those, we do not replace them. This is also not a chat product. I
am not building Slack. I am not building a "team communication
platform." If anyone on the team uses the phrase "team communication
platform" in a pitch deck I will fire them on the spot. We are a
*meeting killer*. That's it. We delete one meeting from your week. We
do that one thing extremely well and we charge for it. (Pricing: I
genuinely don't know yet. Per-seat feels gross for something that
scales with team activity not headcount. Per-team flat feels right but
I'm worried about the 80-person eng org paying the same as the
8-person one. Punt on this, figure it out in month two, but flag it.)
And honestly the success metric I care about, the only one, is: do
pilot teams cancel their standing standup meeting within four weeks of
turning us on? If yes we have a company. If no we have a feature.
That's the bar. Not DAU, not retention curves, not NPS, just: did the
meeting on their calendar get deleted. Binary. Brutal. Correct.

Open stuff I need to figure out before I start writing code tomorrow.
One: is this a Slack app or a web app first? Slack-app-first means
distribution is easier (the digest just shows up where people already
are) but it means we're at the mercy of Slack's app review and we look
like a toy. Web-app-first means we own the experience but no one is
going to log into our dashboard every morning, that's literally the
meeting we're trying to delete. I'm leaning Slack-first with a web app
for the lead's deeper view but I want to argue with someone smart
about this. Two: the "but we LIKE our standup" objection. Some teams
will say this. Some teams will mean it. I genuinely do not know how to
handle that team in sales conversations yet, do I tell them we're not
for them, do I sell them a "standup augmenter" version, I don't know,
and I'm a little defensive about this because the last startup I tried
to do the "we'll be flexible for every customer" thing and it killed
us, we built four products for four customers and shipped none of them
well. Not doing that again. So probably the answer is "we're not for
you, come back when your team is bigger" and walk away from the deal.
That's hard but it's right. Three: how do we handle the engineer who
legitimately wants their work to be illegible to management? That's a
real person. I was that person at my second job. I don't have an
answer yet and it's the kind of thing that bites you in year two if
you don't think about it now.

And another thing, last thing, I promise. I have been BURNED by being
too clever with v1 scope. Last startup I tried to build the whole
platform on day one and we ran out of money with a half-built
everything. Not doing that. v1 is: connects to GitHub, connects to
Slack, connects to one calendar provider (Google, sorry Outlook
people), produces the per-person summary, produces the lead digest,
delivered in Slack DM. That's it. No Linear yet. No Jira yet (Jira is
its own circle of hell and we earn the right to integrate it after we
have ten paying teams). No web app beyond a settings page. Six weeks
to a real pilot with a real team. If I can't get there in six weeks
I'm doing it wrong and I should restart the scope conversation. Okay.
Going to bed. Tomorrow I open a repo and I start. No more thinking.
Done thinking. Goodnight.
