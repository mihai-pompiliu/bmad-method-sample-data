# OneOnOne Walkthrough - Loom Transcript

Loom recording, 8:42, recorded 2026-05-15. Shared with Sam.

Okay hey Sam, thanks for hopping on, or, well, not hopping on, this is async, but you know what I mean.

I'm going to walk you through this thing I've been noodling on for like three weeks now and I really want your gut reaction at the end. Don't be nice. Be useful.

So I'm sharing my screen and you can see I've got this open in, I think this is Figma, or actually, hold on, parts of this are Figma and parts of this are just like a photo of my notebook that I marked up.

So if it looks janky in places that's why. The boxes with the clean borders are Figma. The scribbly stuff is paper. Just bear with the mishmash.

Okay so the product. Working name is OneOnOne. I know, very creative. We can fix the name later.

It's a 1:1 meeting tool for engineering managers. Specifically EMs, not generic managers, although I think it'd work for any people manager honestly, but I want to start with EMs because that's the pain I know.

Here's my whole thesis before I show you the screens.

Every 1:1 tool I've ever used falls into one of two camps. Either it feels like a CRM, like I'm doing pipeline review on my humans, which is gross, or it feels like a journaling app where I'm just dumping text into a void and nothing ever connects to anything else.

Neither is right. The thing I actually do in my head between 1:1s is somewhere in the middle. I'm tracking threads. I'm remembering "oh right, two weeks ago Priya mentioned she felt blocked on the migration, did that resolve."

That's the thing no tool helps with. So that's what I'm trying to build.

Alright, screen one. This is the home screen. You can see right here at the top it's just this week's 1:1s.

Monday, Priya. Tuesday, Marcus. Wednesday, nothing because Jen is on PTO. Thursday, the new hire whose name I keep misspelling. And so on.

The idea is this is the only thing you see when you open the app on Monday morning. Not a dashboard. Not metrics. Just, here are the humans you're talking to this week, in order.

Each row, see this little dot on the left, that indicates whether there are carryover items from last time. Red dot means yes, there's stuff you said you'd follow up on. Gray means clean slate.

I went back and forth on whether to use red, red feels alarming, but Sam, you'll see what I mean when we get to the trends tab, I think the alarming-ness is actually the point.

You should feel a tiny pang of "oh right I owe Priya an answer." Anyway. Movable.

Okay click into a row. This is screen two, the per-direct-report page. So this is Priya's page.

And the layout here, okay so right here you can see three sections stacked. Top section is the running topic list from previous meetings. Like a log. You can see entries going back, I mocked up like six weeks of fake history.

Middle section is this meeting's agenda, which is the thing you'd actually prep before the 1:1.

And then the bottom section, this is the one I'm most excited about, this is the "open carryover items" box. These are action items or threads that haven't been closed out. They just sit there. They follow you around until you deal with them.

Wait let me zoom in on the carryover box because the text is tiny. Hold on. Okay.

So you can see each item has a date stamp of when it first came up, and then how many meetings it's been open. Three meetings open. Five meetings open.

That number, that count, that's the guilt meter. That's the part that makes you actually close the loop.

Sam I know what you're thinking, this is starting to look like Jira for feelings, but stay with me, the next screen is where it gets good.

Screen three. This is the in-meeting view. So you click "start meeting" from the per-report page and you get this split panel.

Agenda on the left, free-form notes on the right. The agenda items you prepped are over here on the left and you can check them off as you go.

The notes panel on the right is just markdown, type whatever, doesn't have to be structured. I want the act of taking notes during a 1:1 to feel like the least friction possible. You're talking to a human. You should not be wrestling with form fields.

And then this button down here, see this little floating button, "log a moment." That's the thing. That's the microcapture.

So like, if Priya says something offhand, like "yeah I've been kinda burned out lately," you don't want to make that a whole agenda item right then because you're in the middle of a different conversation.

You just hit "log a moment," type one sentence, tag it as, I dunno, a friction signal or a praise moment or whatever, and it gets dropped into Priya's running log. And then later, when you're prepping next week's 1:1, it surfaces.

That's the thing I keep coming back to. The "log a moment" button. I genuinely think that's the differentiator.

Everyone else makes you write a whole structured note. I just want to capture the moment and move on.

Okay. Hold on, my mic's clipping, let me back off it a sec.

Better? Okay.

Screen four. This is the trends tab. And Sam, big disclaimer, this is private to the manager. The direct report never sees this. Ever.

Like, not even aggregated, not even anonymized to themselves, nothing. This is the manager's working memory, made visible. It's not HR data. It's not perf data. It's "what patterns am I seeing with this person across time."

So what's on it. You can see three little charts here.

The top one is a mood trend, which, okay, "mood" is the wrong word, it's more like, the ratio of friction-tagged moments to praise-tagged moments over time. So you can eyeball whether things are getting better or worse.

Second chart is topic frequency, which is like, what does Priya keep bringing up. If "scope creep on the migration" shows up in five different 1:1s, that's a signal.

Third one is follow-through rate on action items, which is, of the things we agreed to do, how many actually got closed out. By me, by them, by either of us.

That one I think is super interesting because it surfaces "hey I keep dropping the ball on this person" before it becomes a real problem.

This is the part where I want to be really careful about the privacy model. Like, I do not want this thing to become surveillance.

The trends are for the manager to manage themselves, not to manage the report. I keep going back and forth on whether the report should even know the trends tab exists. Open question. Flag it. Sam, opinions welcome.

Okay last screen. Screen five. Or, well, it's not really a screen, it's a tab on screen two. You can see it grayed out right here. "What to ask this week."

This is the AI thing. The idea would be, given the running log and the carryover items and the moments you've logged, the tool suggests three things to ask in this week's 1:1.

Like "ask Priya how the migration is going, you flagged it two weeks ago and there's been no update" or "Marcus mentioned wanting more architecture work, follow up on that."

I know what you're going to say about the AI tab, just hang with me. I grayed it out on purpose because I am not sure about it. I'm not sure I want AI in this product at all.

The whole point of the product is that the manager does the thinking. If the AI does the thinking, we're back to the CRM problem.

But also, like, realistically, every product has AI now and if I ship without it people are going to ask. So. Open question. This is the part I'm least sure about.

Couple more things and then I'll let you go.

Constraints, things I'm assuming. Calendar integration is table stakes. Google Calendar and Outlook, both, day one.

The home screen pulls 1:1s from your calendar, you don't manually create them. If you don't have calendar integration this whole thing falls apart.

Privacy. Manager-only data. The direct report does not have a login. Like, at all. They are not a user of this product. This is a tool for managers, full stop.

I think that's actually a feature, not a bug, because it means I don't have to design a whole second experience for the report side. But it also means the report has no visibility into what I'm tracking about them, which is, ethically, something I want to think harder about.

Non-goals. This is not a performance review tool. I don't want to be anywhere near comp cycles or calibration.

It is adjacent to OKRs and goal tracking but it is not that. If you want OKRs use the OKR tool. This is the conversation layer, not the goal layer.

What does success look like. Honestly the dream is, a manager uses this for four weeks and tells me their 1:1s feel more productive. That's the squishy version.

The slightly-less-squishy version is, the carryover item count should converge. Like, in week one you've got a bunch of open threads because the tool is finally letting you see them.

By week four that number should be smaller because you're actually closing things out. If carryover keeps growing, the tool isn't working.

Open questions, just so I'm being honest about what I don't know.

One, the AI thing, do we even build it.

Two, pricing, no idea, is this per seat, is this per-report, is this a flat fee, dunno.

Three, does the direct report ever get any visibility, even like a "your manager is using this tool" notice.

Four, is this single-tenant, like an enterprise self-host thing, or is this a SaaS where I'm holding everyone's manager notes in my database, which, yikes, big trust ask.

That's it. That's the walkthrough. Tear it apart.

I really want to know if the "log a moment" thing lands for you or if it sounds dumb when someone else describes it.

Okay, ending the recording. Talk soon.
