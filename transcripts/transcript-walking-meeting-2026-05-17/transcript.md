**A**: Okay so I want to actually use this walk for something, can we talk about Pulse Reports?

**B**: Oh god.

**A**: Yeah I know.

**B**: I knew you were going to bring it up because Marcus put it on the roadmap review again.

**A**: He did, and I keep dodging it, and I can't dodge it anymore because the Q3 planning thing is next Tuesday and I have to walk in with a recommendation.

**B**: [wind] Sorry, what?

**A**: I said I have to walk in with a recommendation Tuesday.

**B**: Right. Okay. So what are you thinking.

**A**: That's what I want to figure out. Like genuinely. Because I've been going back and forth.

**B**: I mean the data is the data.

**A**: The data is bad, yes, but the data is also weird, right? Because if you look at raw MAU on the feature it's like, what, two percent?

**B**: It's under two. I pulled it last week. It's one point seven.

**A**: One point seven. Okay. But.

**B**: But.

**A**: But three of those accounts are Henderson, Brightline, and that one I always forget the name of.

**B**: Cordova.

**A**: Cordova, yes, thank you. And those three together are what, fourteen percent of ARR?

**B**: Something like that. I don't have the exact number but it's in that range.

**A**: Right so we have a feature that one point seven percent of users touch, but the users who touch it are some of our biggest logos and they are in it constantly.

**B**: Constantly is doing a lot of work in that sentence.

**A**: What do you mean.

**B**: I mean I pulled the session data and like, Henderson has maybe four power users. Brightline has two. Cordova has one guy who basically lives in it. So when you say "three strategic accounts use it heavily" what you actually mean is like seven people total use it heavily.

**A**: [pause, crossing street]

**B**: [pause, crossing street]

**A**: Okay that's fair. That's a fair reframe. But those seven people are the seven people who renew the contract.

**B**: Are they though.

**A**: I mean.

**B**: Like have we actually validated that. Because I feel like every time we have this conversation about any feature it's like "oh but the champion uses it" and then it turns out the champion is not actually the economic buyer and we've been propping up something for two years for no reason.

**A**: That's true of Looksy last year, yes.

**B**: That's exactly what I'm thinking about. We spent eighteen months keeping Looksy alive because three accounts allegedly needed it and then when we finally turned it off two of them didn't even notice and the third one asked us to turn it back on and then never logged in again.

**A**: Yeah.

**B**: So I am, you know, professionally skeptical when you say these seven people are load bearing.

**A**: Okay but here's the thing that's different. The Henderson escalation.

**B**: [wind] [inaudible]

**A**: Can you hear me?

**B**: Yeah sorry the wind picked up. Say that again.

**A**: The Henderson escalation. Two weeks ago. Where their VP of Ops emailed Marcus directly because Pulse was rendering slow on the quarterly view.

**B**: I remember.

**A**: That's not nobody. That's a VP at a top twenty account writing to our CRO about this specific feature.

**B**: Okay but counter, the reason it was rendering slow is because the underlying query is held together with duct tape and we've been saying we need to rewrite it for three years and nobody funded it.

**A**: Right.

**B**: So in a way the escalation kind of proves my point, which is that the maintenance burden on this thing is wildly disproportionate to its usage.

**A**: How wildly.

**B**: I had Priya look at it. Roughly one engineer week per quarter just on keeping the lights on. Not building anything. Just bugs and the occasional infra fire.

**A**: That's not nothing but it's not catastrophic either.

**B**: It's not catastrophic in isolation. It's catastrophic when you stack it with the other six things in the same boat. We have like, what, eight features in this zombie state right now? Eight features times one eng week a quarter is two engineers full time doing nothing but propping up stuff nobody uses.

**A**: Okay.

**B**: That's a real number.

**A**: That's a real number, yeah.

**B**: [wind]

**A**: So your vote is kill it.

**B**: My vote is sunset it with a clear migration story and eat the political cost.

**A**: And what is the political cost, in your view.

**B**: Honestly? Marcus yells for a week, the CSMs for those three accounts have an uncomfortable QBR, and then everyone moves on. That's it. That's the cost.

**A**: I don't think that's it.

**B**: Why.

**A**: Because the CRO is going to point out that Henderson is up for renewal in October and Cordova is up for renewal in January and if we sunset a feature they actively use right before a renewal that is a gift to the competition.

**B**: Mmm.

**A**: Like even if the seven users don't actually drive the renewal decision, which, fine, maybe they don't, the optics of "your vendor killed a feature you depend on" is the kind of thing that ends up in a procurement deck.

**B**: That's fair.

**A**: And Marcus will not yell for a week. Marcus will put it in the board update as a risk. And then it's a board level conversation about why product is unilaterally killing features ahead of renewals.

**B**: Okay you're right, I was being glib about the political side.

**A**: [wind] I just don't want to walk into Tuesday and pretend that's a small thing.

**B**: No, I hear you.

**A**: So okay. Kill is hard. What about full invest?

**B**: Full invest meaning what.

**A**: Meaning we treat Pulse as a real product. We fix the query, we modernize the UI, we actually market it, we try to grow usage from one point seven to something respectable.

**B**: How much?

**A**: How much engineering?

**B**: Yeah ballpark.

**A**: I don't know, that's why I'm asking you.

**B**: To do it right? Rebuild the data layer, redo the front end, add the three things in the backlog that the heavy users have been asking for for two years? That's a quarter of work for two engineers. Maybe more.

**A**: That's a lot.

**B**: That's a lot for something we have no evidence anyone else wants.

**A**: Yeah. And if we invest and usage doesn't move, we've just made the zombie bigger.

**B**: We've made the zombie load bearing.

**A**: Right. Okay so kill is politically expensive, invest is operationally expensive. What if there's a third thing.

**B**: I'm listening.

**A**: What if we rebuild it as an opt in beta. Like, we tell the three accounts, hey, we are evolving Pulse, here is the new version, it is in beta, you are our design partners, and we use that as cover to do a clean rebuild and also to gate it so we don't have to support the old surface area.

**B**: Hmm.

**A**: It gives Marcus a story for the renewals, it gives you a clean slate technically, it lets us actually learn whether there's demand or whether it really is just these seven people.

**B**: [wind] [inaudible] hear about this.

**A**: What?

**B**: I said tell me more, but say it again because the wind ate it.

**A**: Okay. Opt in beta. We frame it as an evolution not a sunset. The three accounts get white glove treatment. We get to rebuild the internals. And we get a six month window to decide based on whether beta adoption goes anywhere.

**B**: I don't hate it on its face.

**A**: But.

**B**: But who builds it.

**A**: Right.

**B**: Because the team that owns Pulse is also the team that owns the integrations roadmap and the integrations roadmap is what the rest of the company is screaming for.

**A**: Yeah.

**B**: So when you say "rebuild as a beta" what you are actually saying is "deprioritize integrations for a quarter so we can save a feature seven people use."

**A**: When you put it that way.

**B**: I'm putting it that way because that's how it's going to land with the rest of product when you propose it.

**A**: Yeah.

**B**: And honestly I think you know this because you've been talking yourself out of it as you've been saying it.

**A**: I sort of have, yeah.

**B**: The third option is appealing because it feels like a compromise but it's actually the most expensive option of the three. It's invest, but with a story on top.

**A**: That's, yeah, that's actually a really good way to put it. It's invest with a story on top.

**B**: [wind]

**A**: Okay so I'm back to kill or sunset.

**B**: Right.

**A**: And the difference between kill and sunset is really just timeline and how much hand holding the three accounts get.

**B**: Pretty much.

**A**: So the actual question is, do we have enough information to make this call.

**B**: And I would argue we do not.

**A**: You would?

**B**: Yeah. Because everything we've said in the last twenty minutes has been us inferring what those three accounts will do. We don't actually know. We know seven people log in a lot. We don't know if any of them are the renewal signal.

**A**: Right.

**B**: We have not asked them.

**A**: We have not asked them.

**B**: That seems like a thing we should do before Tuesday.

**A**: I mean Tuesday is in five days.

**B**: Three accounts. Three calls. You and the CSM. Forty five minutes each. That is doable in five days.

**A**: It's doable, yeah. It's not comfortable but it's doable.

**B**: What's uncomfortable about it.

**A**: Just that we are essentially calling them to say "hey we are considering killing the thing you love, what do you think" and that is a weird call to make.

**B**: You don't have to frame it that way.

**A**: How would you frame it.

**B**: I would frame it as, we are doing a roadmap review for next year and we want to understand how Pulse fits into your workflow. Open ended. See what they say. If they say "oh god we'd be lost without it" then that's a real signal. If they say "yeah Bob uses it I think" then that's also a real signal.

**A**: Hm.

**B**: And you walk into Tuesday with three data points instead of three assumptions.

**A**: That's, yeah, that's actually right.

**B**: I do occasionally have good ideas.

**A**: [laughs] You do, occasionally.

**B**: [wind]

**A**: Okay. So my proposal for Tuesday is, I don't give a recommendation Tuesday. I give a plan to give a recommendation in two weeks, with the three customer conversations as the input.

**B**: And in the meantime nobody is touching Pulse.

**A**: Right, no new investment, no sunset announcement, just freeze.

**B**: I can live with freeze for two weeks.

**A**: And the framing for Marcus is, we're being rigorous, we're not making a strategic call on a top twenty account renewal without talking to the account.

**B**: Marcus likes the word rigorous.

**A**: Marcus loves the word rigorous.

**B**: Then use it twice.

**A**: I'm going to use it three times.

**B**: [laughs]

**A**: Okay, what about the support escalation though. The Henderson thing. Because that's still hanging out there and the slow query is still slow.

**B**: That I can patch this week. Priya already has the fix scoped. It's not the rewrite, it's just a band aid, but it'll buy us six months easily.

**A**: Okay so band aid the query, freeze the roadmap conversation, run the three customer calls, regroup in two weeks.

**B**: That's the plan.

**A**: I feel better about that than anything I've thought of this week.

**B**: It's because we walked.

**A**: It's because we walked, yeah.

**B**: [wind] We should do this more.

**A**: We really should. Okay one more loop or are we heading back.

**B**: One more loop. I want to also talk about the integrations thing but that can wait until we're past the construction.

**A**: Deal.

**B**: [pause, crossing street]

**A**: [pause, crossing street]
