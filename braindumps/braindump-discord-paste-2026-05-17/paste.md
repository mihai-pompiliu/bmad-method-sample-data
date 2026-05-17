## --- Day 1 (Monday, 2026-05-12) ---

**marblesoup** _09:14_
ok story time. friday night, prod deploy, trout pushes a bad config bundle, half our services start crashlooping. took us 40 min to manually unwind it because nobody had a clean snapshot of the previous good state. just configmaps and a helm release pinned to the wrong chart version. brutal.

**marblesoup** _09:15_
so. hot take. trout should ship a snapshot + rollback story. one command before deploy, one command to undo. why is this not a thing yet
> :+1: 7 :fire: 3

**dr_taco** _09:22_
oh i love this. been wanting it forever. `trout deploy` already knows everything that's about to change, would be cheap to dump the prior state to disk first

**kbnetes** _09:31_
hmm. love the idea in spirit. nervous about scope. "snapshot cluster state" can mean anything from "yaml of the resources i'm touching" to "full etcd dump + every PVC". we need to pin this down before we sign up for it
> :thinking: 5

**marblesoup** _09:33_
fair. for my use case it's configs and helm releases. i don't need PVCs snapshotted, that's a backup product

**kbnetes** _09:35_
right but someone in this discord WILL ask for PVCs the moment we ship it. just calling it now

**chrissyOPS** _09:40_
+1 to scoping tight. configs and helm seem like the 80% case. anything beyond that is velero territory

**dr_taco** _09:44_
fwiw i think we could do this in like 2 weeks if we keep it to "what trout was about to change, snapshotted, written to a local dir, indexed by deploy id". rollback = re-apply the snapshot

**verysleepyperson** _10:02_
y'all i am SO into this. lost 6 hours last quarter to a bad deploy. would have paid money for an undo button

**kbnetes** _10:11_
ok let me push back gently though. is this a v3 feature or v2.x. because if it's v2.x we need to be REALLY sure it doesn't change any existing command behavior. semver compatibility is a thing i actually care about lol

**marblesoup** _10:14_
new flag, new subcommand, no behavior change to existing flows. should be v2.x in my head

**dr_taco** _10:15_
agree, additive only

**kbnetes** _10:16_
ok i'll buy that. for now.

## --- Day 2 (Tuesday, 2026-05-13) ---

**chrissyOPS** _08:47_
slept on this. one thing i want to flag: helm rollback is already a thing (`helm rollback <release> <rev>`). do we just shell out to helm for the helm parts, or reimplement?

**dr_taco** _08:55_
shell out. 100%. we are not in the business of reimplementing helm's revision history

**kbnetes** _08:57_
agree. trout's job is orchestration. helm does helm.

**chrissyOPS** _09:01_
ok cool. so the "snapshot" for helm bits is really just "remember the release revision number that was live before we deployed". on rollback we call `helm rollback` with that number. that's it

**marblesoup** _09:04_
yes exactly. and for raw kubectl-style configs we keep a yaml dump

**verysleepyperson** _11:30_
random aside. has anyone looked at how kubectl-timetravel does this? they apparently keep a ring buffer of last N applied manifests on disk and let you `kubectl-timetravel undo`. seems close to what we're describing

**dr_taco** _11:34_
oh interesting, link?

**verysleepyperson** _11:36_
i'll dig it up. think it's a side project from someone at one of the cloud providers. point being, prior art exists, we don't need to invent the model

**kbnetes** _11:41_
this is reassuring actually. if there's prior art for the local-disk-ring-buffer pattern we can crib from that and not overthink storage

**chrissyOPS** _13:02_
storage question though. local disk works for solo devs but ops teams will want a shared backing store. s3, gcs, whatever. should that be in v1?

**marblesoup** _13:08_
local default, s3 optional via config. i don't think we need every blob store on day 1

**dr_taco** _13:09_
+1. s3 is fine as the "remote" option for v1. gcs/azure can come later if anyone screams loud enough

**kbnetes** _13:14_
i could live with that
> :+1: 4

**chrissyOPS** _13:20_
also: deploy latency. if snapshotting adds 30 seconds to every deploy, people will turn it off. it has to be fast. like, "you don't notice it" fast

**dr_taco** _13:22_
yeah this is non-negotiable for me too. snapshot has to be near-zero overhead or it's dead on arrival

## --- Day 3 (Wednesday, 2026-05-14) ---

**marblesoup** _07:55_
ok i wrote up a rough rfc last night. very rough. https://gist.github.com/marblesoup/snapshot-rollback-trout-rfc-draft
have a look, tear it apart

**dr_taco** _08:30_
reading now

**dr_taco** _08:42_
overall yes. one thing: you have PVCs listed under "stretch goals". i'd move them to non-goals for v1, full stop. "stretch" invites scope creep

**marblesoup** _08:44_
fair, will edit

**kbnetes** _09:10_
read it. broadly agree with direction. the PVC thing though, i want to argue for. SOME workloads are stateful and rolling back the config without rolling back the volume state leaves you in a worse place than before

**chrissyOPS** _09:14_
counter: rolling back PVCs is a backup/restore problem, not a deploy problem. if your stateful workload needs volume rollback you need velero or your csi driver's snapshot story. trout shouldn't be in that game

**kbnetes** _09:18_
i hear you but the user experience is going to be "i ran trout undo and my database is still broken"

**dr_taco** _09:22_
that's a docs problem imo. clearly say "rolls back configs and helm releases, does not touch persistent volumes, use velero for that". if we try to do PVCs we will be doing it for the next 18 months

**verysleepyperson** _09:30_
strong +1 to docs/explicit-non-goal. one of the things i like about trout is it doesn't try to be everything

**kbnetes** _09:45_
mmm. ok. i'll come around. PVCs out for v1. but i want the rfc to have an explicit "future work" section that calls out the stateful workload gap, so we're honest with users

**marblesoup** _09:46_
deal. adding that section now

**chrissyOPS** _09:48_
this is good. feels like we're actually converging
> :+1: 6 :sparkles: 2

**dr_taco** _10:30_
on storage: rfc says "local default, s3 optional, bring your own bucket". i think that's the right call. anything more than s3 for v1 is overreach

**marblesoup** _10:32_
that's where i landed too

**verysleepyperson** _14:11_
re: success criteria. how do we know this feature is actually working out there? like, what's the proof it solved something

**marblesoup** _14:14_
good q. couple ideas: (1) we see actual adoption among power users, like 3+ teams using it within first couple months. (2) we get fewer "i broke prod and had to manually unwind" tickets

**chrissyOPS** _14:17_
both feel measurable. i like it.

## --- Day 4 (Thursday, 2026-05-15) ---

**dr_taco** _08:01_
ok bikeshed time. the command. options on the table:
1. `trout undo`
2. `trout rollback`
3. `trout snapshot restore`

**dr_taco** _08:02_
i vote `trout undo`. it's the most obvious to a panicked user at 2am

**marblesoup** _08:04_
strong agree. nobody at 2am types "snapshot restore"

**kbnetes** _08:09_
`rollback` is more accurate to what it does. `undo` implies it works on any action, which it doesn't

**chrissyOPS** _08:11_
counter-counter: the user doesn't care about technical accuracy at 2am. they care about "make it stop". `undo` wins on that axis

**dr_taco** _08:13_
also `undo` is short. people will type it more

**kbnetes** _08:20_
ok fine. `undo`. but document it as "undoes the last trout deploy" so nobody thinks it undoes arbitrary cluster actions

**marblesoup** _08:21_
yes, that's the right framing

**verysleepyperson** _08:30_
> :+1: 5

**verysleepyperson** _08:31_
agreed, `trout undo` is the move

**chrissyOPS** _11:02_
ok next question that's been nagging me. storage. if we offer s3 as a managed thing, does this become a paid cloud feature eventually? like "trout cloud stores your snapshots for you, $9/mo"?

**dr_taco** _11:08_
oh hmm. interesting. i don't hate it but i don't want to design v1 around monetization. bring-your-own-bucket for now, see if anyone wants managed later

**marblesoup** _11:10_
that's how i'd play it too. ship the open thing, see what people ask for

**kbnetes** _11:14_
+1. don't put monetization on the critical path of a v1 ux decision

**chrissyOPS** _11:15_
ok yeah, fair. just wanted to flag it before we built ourselves into a corner

**verysleepyperson** _16:40_
random thought: should `trout deploy` auto-snapshot by default, or opt-in via flag?

**marblesoup** _16:45_
default on. the whole value prop is "i didn't have to remember to snapshot"

**dr_taco** _16:46_
default on, with a `--no-snapshot` escape hatch for the people who really don't want it

**kbnetes** _16:48_
agree. defaults matter

## --- Day 5 (Friday, 2026-05-16) ---

**marblesoup** _09:00_
ok i think we've actually landed somewhere. let me try to summarize where we are:

**marblesoup** _09:01_
- trout adds snapshot + rollback. snapshot captures configs and helm release revisions before each deploy. rollback (`trout undo`) reapplies the snapshot in one command
- snapshot is on by default, `--no-snapshot` opts out
- storage: local disk by default, optional s3 via config (bring your own bucket)
- PVCs are explicitly out of scope for v1. documented as future work. velero is the answer for stateful workloads today
- helm rollback is delegated to helm itself (we just remember the revision number)
- ships as v2.x because it's additive, no behavior change to existing commands
- has to be near-zero added latency on deploy, this is a hard constraint
- success looks like: a handful of power users adopting within ~60 days, and fewer "i broke prod manually unwinding" support pings

**marblesoup** _09:03_
filed an issue tracking this: https://github.com/trout-cli/trout/issues/847

**dr_taco** _09:10_
this is a great summary. lgtm

**chrissyOPS** _09:14_
+1, ready to start prototyping
> :+1: 6 :rocket: 3

**verysleepyperson** _09:20_
hyped. happy to test once there's a branch

**kbnetes** _10:30_
i'm going to register one ongoing dissent for the record. i still think v1 without ANY stateful workload story is going to confuse users badly. i don't have the votes to block it and i'm not trying to, but i'm going to write a separate issue documenting my concerns so we have a paper trail when people complain. just want it on the record

**marblesoup** _10:33_
totally fair, that's healthy. please do file it, would rather have your concerns documented than not

**kbnetes** _10:34_
will do today

**dr_taco** _10:40_
appreciate that approach honestly. shipping with eyes open > pretending we solved everything

**chrissyOPS** _11:00_
ok then. snapshot-and-rollback, v2.x, configs + helm only, local-or-s3, `trout undo`, default on. let's build it
> :+1: 8 :trout: 4

**verysleepyperson** _11:02_
lol who added a :trout: emoji

**chrissyOPS** _11:03_
that was me, several weeks ago, no regrets
