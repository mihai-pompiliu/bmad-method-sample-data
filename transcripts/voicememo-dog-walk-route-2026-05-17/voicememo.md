# Voice Memo, 2026-05-17, 7:42am

ok recording, recording, um

so I'm walking Bowie right now and I just, I had this thought, like

we just turned onto Linden and there's, you know that house with the geese? the guy with the geese? he's out there again

and I was thinking, like, I should be able to warn people about that

[traffic sound]

sorry, car

so basically, ok here's the idea, an app for, um, for sharing dog walk routes. like specifically dog walks. because

because Strava is, you know, Strava is for runners and cyclists and it's all like, your pace and your splits and I don't care about my splits, I'm walking a 14 year old beagle, my splits are bad, that's not the point

and AllTrails is great but AllTrails is like, drive 40 minutes to a state park and do a 6 mile hike. I want to know about the loop around my actual block

and Nextdoor, ugh, Nextdoor is just, you know, Nextdoor is people complaining about packages and, like, suspicious teenagers, I don't want to be on Nextdoor

so. dog walks. specifically. um

like

I keep thinking, somebody must have built this, right? like surely. but every time I look, it's either, you know, it's a Facebook group, which, no, or it's a subreddit, or it's, like, a Google map that one person made and shared in their neighborhood email list. there's no, there's no app

and I've been walking Bowie for, what, 14 years, in like four different neighborhoods, and every single time we move I have to relearn, where are the good walks, who has the loose dog, where's the shade in summer, where's the ice in winter

and the people who know are the other dog owners and you only meet them by, you know, randomly bumping into them on the sidewalk

ok anyway, what would it actually do though, ok so

you'd open it and you'd see a map and on the map there'd be routes, like, routes that other people in your neighborhood have walked with their dogs and saved. and you could tap one and see, like, photos along the route, oh that's important, photos. because

[pause]

because like right now I'm passing this really pretty stretch with the dogwoods and if I were recommending this route to someone I'd want them to see, here's what it looks like

and then, hazards. hazard pins. so like right here, geese guy, I'd drop a pin, "aggressive geese, owner doesn't care, avoid before 9am"

um what else, broken glass by the bus stop on Maple, that's a pin

the off-leash dog at the blue house on Cedar, pin

and recommended times, oh that's a good one, like the elementary school courtyard, you can cut through after like 4pm when school's out and it's empty and it's this great open space for Bowie to sniff around but if you go at 8am you're going to get yelled at by a crossing guard

so, time-of-day notes on routes. yeah

and weather, kind of, like, "this route is bad in rain because the underpass floods", that's a note, but maybe that's just, that's just a hazard pin, that's not a separate feature, ok fine, hazard pins cover it

um where was I, ok features

map, photos, hazard pins, time notes, and

oh, terrain and energy level. because Bowie is old, he needs flat, no stairs, shade, benches to stop at. but if I had a young border collie I'd want, you know, hills, distance, somewhere they can really go

so each route should have like a tag, senior dog friendly, high energy, puppy safe, that kind of thing

and a filter, obviously, like I want to filter for "senior dog friendly within 1 mile of me"

within 1 mile, that's the other thing, the scope. it has to be neighborhood scope. I don't want to see a great route in like, Wilmington, that's not useful, I'm not driving Bowie to Wilmington for a walk. so like, default radius, half mile? mile? I don't know, configurable maybe

walking distance, that's the unit. like, if I can't walk to the start of the route from where I am, it shouldn't be in the default view. maybe a separate tab for "drive to it" routes, like if you're traveling, you're in a new city with your dog, ok that's a use case actually, traveling with your dog and you want to know where to walk near your hotel, hmm

but, ok, MVP is the neighborhood case. the travel case is later

ok now the part I keep going back and forth on

do users have profiles. like do you have a profile that's you. or is it just, routes exist and you tap on them

and then, separately, do DOGS have profiles. because part of me is like, yeah, Bowie has a profile, here's Bowie, he's a beagle, he's 14, here are the routes that work for him

but then part of me is like, that's, that's getting into Instagram-for-dogs territory and I don't, I'm not trying to build that

wait actually, hmm

ok scratch that, what if dog profiles are optional. like you can just be a user with routes, or you can add a dog and pin routes to that dog. that, that might work. yeah. optional dog profiles

um can other people rate your route. like five stars. ehhh

I don't, I don't love that, that feels gross, like it turns into a popularity contest and then people are gaming it and

actually no wait, ratings would be useful because if I'm looking at someone's route and 30 people said "yeah this is great" vs zero people, that's signal

but, ok, maybe not stars, maybe just like, a "walked it" button. like Goodreads "read" or whatever. you walked it, you can leave a note. and the count of "walked it"s is the signal, not a star rating

yeah I like that better, walked-it count instead of stars

and comments, maybe, like, you walked it and you can add a comment, "geese were chill today" or "construction on the south end, route closed", you know, fresh info from people who actually walked it recently

oh recency, recency matters. like a hazard pin from three years ago is probably stale. so maybe pins decay, or you have to confirm them, like "is this still here?" and if nobody confirms it for 90 days it greys out

that's a thing. pin freshness. ok

ok what else

[pause, Bowie sniffing]

come on bud

um, monetization. I have no idea. like, free? freemium? I don't know

actually it'd be free, definitely free at first because nobody's going to pay for an app they haven't tried, well, free for like, the first three routes you save, then maybe five bucks a month for unlimited? I don't know, let me think about that, that might be wrong, maybe it's all free and it's ad supported but I hate ads, ugh, I don't know, park that

privacy. oh god, privacy. because routes ARE location data. like, if I save my daily route, I'm telling the app where I live, basically, because the route starts at my house. um

I don't want to build a thing that, you know, doxxes people. so there's gotta be some, like, fuzz the start point, or let people start the recording a block from home, or, I don't know, this is a real question I don't have an answer for

ok and last thing, platform. iOS only to start? or, like, do I do React Native and do both? because Android users exist, but, you know, MVP, maybe iOS, ugh I don't know, ask someone

and how do routes even get created, do you record them by walking with the app open, GPS tracking, like Strava does, or do you draw them on a map after, or both, hmm. probably record-while-walking is the easier UX but it eats battery and I'm already, you know, my phone is at 60% by the time I get home from a walk as it is

(squirrel, Bowie, no, leave it)

um also, social, like, do you follow other dog owners, or is it just, here are routes near you and you don't really know who made them, I think, I think follow is too much, that's the Instagram problem again, just, surface routes by proximity and recency and walked-it count, that's enough

oh and seasons, that's a thing, like a route that's great in October because of leaves is miserable in July because no shade, so maybe routes should have seasonal notes too, or photos with dates so you can see, you know, here's what it looks like in spring, summer, fall, winter, that'd be cool, that's a v2 thing probably

ok the basics, let me try to summarize before I forget

an app for, dog owners, to share local walking routes, with, photos, hazard pins, time-of-day notes, terrain and energy level tags, neighborhood-scoped, with optional dog profiles, and a "walked it" thing instead of stars

problems it solves, Strava too sporty, AllTrails too car-trippy, Nextdoor too dramatic, nothing exists for "what's a nice walk for my specific dog right here"

um

name, what would I even call it. PawPath. ugh, no. RouteRover. worse. ok I'll figure out the name later, names are the last problem

ok Bowie's done, we're heading back, end memo
