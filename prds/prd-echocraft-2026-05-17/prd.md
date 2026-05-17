# EchoCraft — Product Requirements Document

**Document type:** PRD (game-flavored / GDD-leaning)
**Product:** EchoCraft — a two-player asymmetric co-op browser puzzle game
**Version:** 0.3 (draft)
**Date:** 2026-05-17
**Owner:** Game Design Lead

---

## 1. Vision / Core Fantasy

EchoCraft is a game about seeing what the other person cannot. Two players stand in the same impossible room, but only one of them can see the room as it is. The other sees only the *echo* of the room — pale, drifting after-images of every object that has ever been moved, lingering in the air like breath on a cold window. Neither player can solve the puzzle alone. They must speak — out loud, on a phone call, across a couch, through a closed door — and braid their two half-views into a single shared understanding.

The core fantasy is the small, delighted gasp of synchronized cognition: the moment when one player says *"the heavy thing is drifting left now"* and the other says *"yes — and I can see where it used to be, push it back"* and the puzzle resolves with a soft chime. EchoCraft is intimate, low-stakes, and unapologetically about the conversation between two humans. The game itself is the thinnest possible scaffolding around that conversation.

---

## 2. Target Audience

- **Primary:** Casual co-op duos — couples, siblings, roommates, long-distance friends — who already play games like *Keep Talking and Nobody Explodes*, *We Were Here*, *It Takes Two*, and *Portal 2 co-op*.
- **Secondary:** Puzzle gamers (Baba Is You, Patrick's Parabox, The Witness) looking for a lighter-weight, social variant of the genre.
- **Tertiary:** Streamers and content creators — asymmetric-info games perform well on streams because the audience can see both views and the comedy of miscommunication.
- **Anti-audience:** Solo players, competitive players, players who want voice chat built into the game. EchoCraft will feel hollow alone and frustrating to anyone who refuses to talk.

Expected session length: **5–15 minutes per puzzle**, with most duos completing 2–4 puzzles per sitting.

---

## 3. Core Mechanics

This is the densest section of the document. It describes the asymmetric-view system, the object physics, the verbal-coordination loop, and how puzzle complexity scales.

### 3.1 Asymmetric View

The same 3D room is rendered to two players with two completely different shaders and object sets:

- **Player A — the Solid:** Sees all physical objects (crates, levers, pressure plates, doors, the player avatars themselves) in full color and full lighting. Does **not** see echoes. The room looks like a normal puzzle room.
- **Player B — the Echo:** Sees the room as a translucent volumetric wireframe. Physical objects are invisible. Instead, B sees **motion echoes** — semi-transparent ghost-trails showing every position an object has occupied in the last ~8 seconds, fading along a gradient from bright (recent) to nearly invisible (oldest). Stationary objects produce no echo and are therefore *completely invisible* to B.

Neither player can switch perspectives. The asymmetry is locked for the duration of a puzzle. Players can voluntarily swap roles between puzzles.

### 3.2 Object Physics

Objects in the room obey a simplified rigid-body physics model:

- **Crates** — pushable in cardinal directions, mass affects push speed, can be stacked (max 3 high).
- **Spheres** — roll continuously after impulse; useful for puzzles requiring sustained motion (and therefore sustained echo).
- **Levers / buttons** — bistable; produce a short echo "pulse" when toggled.
- **Pressure plates** — require a minimum mass; "stale" plates (held by a stationary crate) produce no echo and become invisible to Echo player.
- **Drift fields** — designated zones where objects gently float, producing continuous echoes even without player input. Used to keep otherwise-invisible objects visible to the Echo player.

Critically: **only motion produces echoes.** This is the central design tension. The Solid player can place a crate perfectly and walk away, but the moment they stop touching it, the Echo player loses sight of it. Most puzzles require *negotiated, sustained motion*.

### 3.3 Communication Mechanic

The game provides **no in-game chat, no voice, no emotes, no pings.** This is deliberate and load-bearing. Players are expected to communicate via:

- Phone call / video call (long distance)
- Same room, talking out loud (couch co-op on two devices)
- Discord, FaceTime, or whatever they already use

The absence of in-game communication is a feature, not an omission. The game's title screen explicitly tells players: *"Open a phone call with your partner before pressing Start."*

### 3.4 Puzzle Progression

Puzzles escalate along three axes:

1. **Spatial complexity** — number of objects, room size, number of rooms.
2. **Echo decay pressure** — early puzzles give 8-second echo trails; late puzzles drop to 3 seconds, forcing faster verbal coordination.
3. **Asymmetric dependency** — early puzzles can almost be solved by Solid alone with Echo confirming; late puzzles *require* both players to act simultaneously while describing their actions.

A puzzle is solved when a target condition is met (door open, all plates pressed, sphere in goal) and the game plays a soft three-note chime audible to both players.

---

## 4. Aesthetic Direction

- **Visual style — Solid view:** Soft, papery, slightly desaturated. Think *Monument Valley* meets *Lume*. Warm interior lighting, muted pastels, subtle paper-grain shader. Objects feel handcrafted and gentle.
- **Visual style — Echo view:** Deep blue-black void. Echoes rendered as glowing cyan-to-violet gradient trails with a faint volumetric bloom. Echo view should feel like staring into a frozen lake at night.
- **Typography:** A single humanist sans (Inter or similar) for UI. No diegetic text in the world.
- **Audio cues:**
  - Object motion produces a soft wooden *thunk* in Solid view and a corresponding bell-like chime in Echo view. **Both players hear both sounds** — audio is the one channel that is NOT asymmetric, which gives players a thin shared sensory thread.
  - Ambient room tone: low pad with occasional wind-chime accents.
  - Solve chime: three ascending notes in C major, ~1.5 seconds.
- **Mood:** Quiet, contemplative, intimate. Closer to a meditation app than an arcade game. No timers, no fail states, no leaderboards.

---

## 5. Feature Requirements

> *Note: these intentionally mix capability-level requirements with implementation detail. Some items prescribe how; others describe what. This will be cleaned up before engineering handoff.*

### FR-1: Matchmaking via shareable URL
Player A clicks "Start co-op," receives a short URL (e.g., `echocraft.io/r/wisp-otter-83`), sends it to Player B via any channel. B opens the URL, picks a role, and the room loads. No accounts, no login, no lobby browser. URLs expire after 24 hours of inactivity.

### FR-2: Role selection
On joining, B is presented with two cards: "I'll see the objects" (Solid) and "I'll see the echoes" (Echo). A sees a waiting screen until B picks. Roles can be swapped between puzzles via a "Switch Sides" button on the post-puzzle screen.

### FR-3: WebSocket-based state sync at 30Hz
Server-authoritative physics simulation runs at 30Hz, broadcasts object transforms to both clients. Client-side interpolation smooths to 60fps render. Justified by the need for both players to see motion echoes derived from the same canonical position stream.

### FR-4: Echo trail rendering
Echo trails are rendered as a custom WebGL shader that samples the last N position snapshots per object and renders them as a tapered, alpha-gradient ribbon. N defaults to 240 samples (8 seconds at 30Hz) and is tunable per-puzzle.

### FR-5: Mobile-friendly controls
- Solid player: virtual joystick (left thumb) + tap-to-interact (right thumb). Pinch-zoom and two-finger rotate camera.
- Echo player: same control scheme. Echo player still has an avatar in the room — they just can't see the static geometry.
- Minimum supported viewport: 360x640. Tested on iPhone SE (2nd gen) and Pixel 4a.

### FR-6: Puzzle progression / level select
After solving a puzzle, players see a level-select screen with the next 1–2 puzzles unlocked. Puzzles are grouped into "Chapters" of 4–5 puzzles. Progress is saved per-URL in browser localStorage and on the server for 30 days.

### FR-7: Reconnect tolerance
If either player drops connection, the room is preserved for 5 minutes. Either player can rejoin via the same URL. Object positions are frozen during a disconnect (no echo decay).

### FR-8: Accessibility — colorblind mode
Echo trail gradient swappable between cyan-violet (default), yellow-magenta, and white-orange. Audio cues are essential and cannot be disabled (they are the only shared sensory channel).

### FR-9: No text chat anywhere in product
This is a feature requirement: the absence of chat must be preserved through all future iterations. The product principle is that verbal communication is the game.

### FR-10: Tutorial as Puzzle 0
The first puzzle is a tutorial disguised as a puzzle. It teaches the asymmetric view by forcing one trivial interaction (push one crate onto one plate) and nothing else.

---

## 6. Level Progression — Example Puzzles

A representative slice of the first two chapters:

### Puzzle 1 — "Hello, Stranger"
One room, one crate, one pressure plate, one door. Echo player guides Solid to the (invisible-to-Solid-when-still) crate by saying where the recent echo trail starts. Teaches: echoes show recent motion; speech is required.

### Puzzle 2 — "Drift"
A drift field in the center of the room slowly floats a sphere in a slow circle. Echo player can always see the sphere (continuous motion = continuous echo). Solid must intercept it at the right moment to knock it onto a plate. Teaches: drift fields, sustained motion.

### Puzzle 3 — "Three Plates, Two Hands"
Three plates, two crates, one sphere. Players must coordinate which object goes where; one object must remain in motion to stay visible to Echo. Teaches: simultaneous action, sustained motion as a resource.

### Puzzle 4 — "The Stale Room"
A room with several crates that were "pre-placed" before the puzzle starts — they have no echo and are invisible to Echo player. Solid must nudge each one slightly so Echo can build a mental map. Teaches: echoes as a deliberate communication tool, not just a side effect.

### Puzzle 5 — "Two Rooms, One Door"
Players are in adjacent rooms separated by a one-way mirror they cannot see through. They share audio cues only. Each must solve a half-puzzle whose solution depends on timing the other's actions. Teaches: trust, audio-only coordination.

### Puzzle 6 — "Decay"
Echo trail duration drops to 4 seconds. The puzzle is mechanically simple (two crates, two plates) but requires faster verbal coordination. Teaches: time pressure on communication.

### Puzzle 7 — "The Swap"
Mid-puzzle, the game forces players to swap roles. The room state persists, but each player must now interpret what they were just describing from the other side. Teaches: empathy, perspective-taking. Chapter 2 finale.

---

## 7. Technical Constraints

- **Platform:** WebGL 2.0 in modern browsers (Chrome 110+, Safari 16+, Firefox 110+). No native app.
- **Engine:** Three.js or Babylon.js (TBD by tech lead). Custom shader for echo trails.
- **Networking:** WebSocket to a Node.js + uWebSockets server. Server-authoritative physics via a lightweight rigid-body lib (cannon-es or rapier-wasm).
- **Latency budget:** End-to-end action-to-echo latency must stay under **150ms** at the 95th percentile. Above this threshold, verbal coordination ("push it now") becomes frustrating because the Echo player sees the motion noticeably after the Solid player describes it.
- **Bandwidth:** Target under 30 KB/s per client at steady state. Object transforms delta-encoded.
- **No voice or chat infrastructure:** The product explicitly does not run any voice/video/text relay. Players bring their own communication channel. This is also a cost and privacy advantage.
- **Hosting:** Single-region launch (us-east) for closed beta. Multi-region required before public launch to keep latency budget across distant duos.
- **Mobile performance:** Must hold 30fps render on iPhone SE (2nd gen) with 6 dynamic objects on screen. Echo shader is the main risk; will require LOD on trail sample count.
- **No accounts:** Anonymous play only at launch. Optional account system deferred to post-launch.

---

## 8. Success Signal

EchoCraft succeeds when players experience the magic of asymmetric collaboration — when the conversation between the two players becomes the game, and the puzzles fade into the background as a substrate for shared discovery. We will know we have it right when duos finish a session and immediately want to play another one, when they talk about the game to other duos in terms of how it felt to communicate, and when streamers find the asymmetry inherently watchable. The product is working when the verbal channel feels alive.

---

## 9. Open Questions

- Should puzzle 7 ("The Swap") be optional or mandatory? Forced perspective-swap may frustrate some duos.
- Is 8 seconds the right default echo duration, or should it be 6? Needs playtesting.
- Should we offer a single-player "practice mode" where one human plays both roles on a split screen? Arguments both ways: it expands the audience but dilutes the core fantasy.
- How aggressively do we lean into the "no chat" principle in marketing? Risk: confuses players who expect built-in voice. Opportunity: differentiates sharply from every other co-op game.

---

*End of document.*
