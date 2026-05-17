# StemLoop PRD

- Product: StemLoop
- Type: Audio plugin (AU, VST3, AAX) for desktop DAWs
- Status: Draft
- Date: 2026-05-17
- Owner: Product

## Problem

Producers who want to flip a sample, remix an old track, or pull a vocal out of a reference loop currently leave their DAW, upload the file to LALAL.AI or Moises, wait for cloud processing, download five WAVs, re-import them, line them up to the grid, and only then start making music. The round-trip kills flow. It also forces a commit on stem choices before any creative work happens. If a producer realizes the bassline is what they actually wanted (not the vocal), they upload again and wait again. The tools work but they are batch tools pretending to fit a real-time creative process.

The other path, doing it by ear with EQ and sidechain tricks, sounds bad and takes hours.

There is also a privacy and ownership angle. Uploading unreleased work to a third-party server, even one that says it deletes files after processing, is something a lot of producers and engineers will not do for client material. A local solution removes that conversation entirely.

## Target user

Hip-hop producers, lo-fi beatmakers, electronic music producers, remixers, and sample-flippers who already work in Ableton, Logic, FL Studio, or Reaper. They own at least three paid plugins. They are comfortable with bus routing and parameter automation. They expect plugins to feel native to the host. They will not tolerate a cloud-call sitting in the middle of their session.

Secondary user: mix engineers and post-production audio engineers who occasionally need to pull a problem element out of a stereo print (a bleed, a cough, a hum-source in the harmonic stem) without re-tracking. They care more about render-mode quality than real-time and they will use the offline path almost exclusively.

Not a target user (for v1): film composers working at 96kHz with strict latency budgets, broadcast engineers, mastering engineers. The plugin is not aimed at them and will not be optimized for their workflows.

## Functional requirements

- FR1: Isolate five stems from a stereo input. Vocals, drums, bass, harmonic instruments, other or unclassified.
- FR2: Per-stem solo, mute, and level fader. All three are host-automatable.
- FR3: Real-time processing at host buffer sizes of 64, 128, 256, 512, and 1024 samples. Output is sample-accurate at the reported plugin delay.
- FR4: Render-mode toggle. Trades real-time for higher separation quality on bounce or freeze.
- FR5: Stereo width preservation per stem. Mono collapse is opt-in, not default.
- FR6: A/B preview button to flip between processed and dry signal without losing fader state.
- FR7: Smart tooltips on load. The plugin samples a few seconds of input and suggests a primary stem ("vocal-heavy, route vocals to bus 1"). Suggestion only, never auto-applied.
- FR8: Preset system with per-genre defaults (hip-hop, house, rock, lo-fi, acoustic, podcast or speech).
- FR9: Multi-output bus exposes the five stems as discrete outputs the host can route to separate tracks.
- FR10: Drag-and-drop bounce. Each stem can be dragged from the plugin GUI directly to the host arrangement as a rendered audio file. Uses the host's drag-export protocol where available (Ableton, Bitwig, FL Studio) and falls back to a temp WAV file otherwise.
- FR11: Undo-aware preset recall. Switching presets is a single undo step in the host, not five separate parameter changes.
- FR12: Sidechain input on the second stereo bus. Producers can feed a reference track (the clean vocal, the clean drum loop) to inform separation. Optional, not required for basic use.
- FR13: Latency report toggle. Producers can flip the plugin between low-latency mode (smaller lookahead, slightly worse separation) and quality mode (larger lookahead, full reported PDC). Both are real-time-capable.

## Plugin surface

- Formats: AU, VST3, AAX. No VST2.
- Architectures: macOS Universal (Intel and Apple Silicon), Windows x64. No 32-bit.
- DAW host targets at v1: Logic Pro, Ableton Live 11 and 12, Pro Tools 2023+, FL Studio 21+, Reaper 7+, Cubase 13+, Bitwig 5+.
- Plugin category: audio effect with multi-output bus configuration (one stereo in, five stereo outs).
- Automatable parameters: per-stem solo, per-stem mute, per-stem level, A/B toggle, render-mode toggle.
- MIDI: none. No notes in, no notes out, no MIDI CC.
- GUI: resizable window, retina and high-DPI aware, scales from 800x500 to 1600x1000. Remembers last size per session.

## Audio-thread constraints

- The real-time audio callback is allocation-free and lock-free. No mutexes, no `new`, no file I/O, no logging on the audio thread.
- Inference runs on a dedicated worker thread. The audio thread reads from a single-producer single-consumer ring buffer.
- Added latency budget: 32ms at 44.1kHz. Scaled proportionally at 48, 88.2, 96, and 192kHz. The plugin reports correct PDC (plugin delay compensation) to the host so downstream tracks stay aligned.
- Sample rate handling: native support for 44.1, 48, 88.2, 96, 192kHz. Internal model runs at 44.1 with resampling at the boundary.
- CPU budget: under 18 percent of one Apple M2 performance core on a typical full-band music track at 44.1kHz with a 256-sample buffer. Windows equivalent target: Ryzen 7 5800X, same buffer.
- Denormal protection on. FTZ and DAZ flags set on the audio thread.
- Graceful degradation: if the worker thread falls behind (CPU spike, host glitch), the audio thread outputs the dry signal for the affected block rather than dropping samples or clicking.
- Multiple instances on the same project share a single inference worker pool where possible, so 8 instances do not spin up 8 model copies. Memory ceiling: 600MB resident across all instances in a session.
- SIMD: AVX2 on Windows x64, NEON on Apple Silicon. SSE4.2 fallback for older Intel Macs while we still support them.

## Distribution

- Direct download from stemloop.audio. No third-party reseller in v1.
- Installer is a signed and notarized PKG on macOS and a signed MSI on Windows. Both bundle the ML model (~180MB) and unpack to the standard plugin paths (`/Library/Audio/Plug-Ins/Components`, `/Library/Audio/Plug-Ins/VST3`, etc.).
- License: machine-bound, validated against our own licensing service. One purchase covers two activations. Activations can be transferred from a web account dashboard.
- No iLok in v1 (see open questions). The argument against iLok is friction at install. The argument for is that pro users on Pro Tools expect it.
- Trial: 14 days, fully functional, watermark beep every 30 seconds on the processed output. Trial does not require credit card.
- Update channel: in-plugin "check for update" plus opt-in auto-update on launch of the host.
- Uninstaller is shipped and visible. Removes plugin binaries, the bundled model, and the license file. Producers who try a plugin and bounce should not have to grep `/Library` to clean up.

## Pricing and licensing

- $129 one-time purchase. Includes all v1.x updates. v2 will be a paid upgrade at a discount for existing owners.
- $9/month subscription as an alternative. Includes all updates while active. Cancellation reverts to trial mode with the watermark beep.
- Education discount: 40 percent off the one-time price with a verified .edu address.
- Offline-first. License check happens at install and then every 14 days against the licensing service. A failed check after 14 days drops the plugin to trial mode (functional with watermark) rather than blocking entirely. We do not strand a producer mid-session.

## Constraints

- Real-time stem separation has a known quality ceiling versus offline. The render-mode escape hatch exists because of this. Marketing must not claim "studio-quality separation in real time" without the render-mode caveat.
- The bundled model is ~180MB. The installer must handle this gracefully on slow connections and on machines low on disk. Pre-flight check for 500MB free before unpacking.
- No internet connection required at runtime after install. Licensing pings are background, non-blocking, and never gate audio.
- The audio thread is the hard constraint. Any feature that cannot meet the RT-safe rules is render-mode-only.
- AAX requires Avid Developer Partner certification. That gates Pro Tools shipping and adds calendar time we do not fully control.
- Apple notarization, hardened runtime, and entitlements add their own variability to release timing. We assume one notarization rejection cycle per major release in our planning.

## Non-goals

- No standalone app in v1. DAW plugin only.
- No cloud rendering. Local inference is the differentiator.
- No MIDI generation from audio. Audio-to-MIDI is a different product category.
- No built-in EQ, compressor, reverb, or other effects. Stems output clean. Users apply their own chains downstream. That is what their DAW is for.
- No iOS or iPad in v1. AUv3 deferred to v1.x.
- No training on user audio. Nothing leaves the machine. This is a privacy stance and a marketing position.
- No support for Windows on ARM in v1.
- No support for hosts older than the listed minimums.
- No batch processing UI. The plugin operates one instance at a time on one input track. Batch jobs are a different product.
- No streaming-source input (Spotify, YouTube). Audio in is whatever the DAW routes to the plugin. Sourcing rights for streaming audio is the user's problem, not ours.
- No live-input optimization for performance use. The plugin can run on a live input, but we are not targeting the live-rig market in v1 and we will not advertise it.

## Success signal

In the first 90 days post-launch:

- 5,000 paid activations across one-time and subscription, blended.
- Median trial-to-paid conversion above 12 percent.
- Crash rate under 0.1 percent of plugin instantiations across all hosts and platforms.
- Net Promoter Score above 40 from in-plugin survey after 30 days of use.
- At least three unsolicited mentions in producer YouTube channels (Andrew Huang, In The Mix, You Suck at Producing tier).
- Pro Tools share of installs above 8 percent. If it is lower, iLok was probably the reason and we revisit.
- Render-mode usage above 30 percent of bounces. If it is near zero, real-time quality is good enough and render-mode is over-built. If it is dominant, real-time is not living up to the pitch and we have a model problem.
- Drag-and-drop bounce used in at least 20 percent of sessions where the plugin is loaded. If it is unused, we built a feature producers do not want and we cut it from v1.1.

## Open questions

- iLok or no iLok. Pro Tools-heavy users expect it. Hip-hop and electronic users hate it. Do we ship with our own licensing only and add iLok as an option in v1.1, or eat the friction up front for credibility with the Pro Tools crowd?
- Render-mode quality gap. How wide is acceptable? If real-time is 80 percent of render-mode quality, that is fine. If it is 60 percent, the product feels like a demo of the render-mode product.
- Multi-out bus support varies by host. Logic and Pro Tools handle it cleanly. FL Studio and Reaper are quirky. Do we ship a fallback mode (single stereo output with a stem-selector parameter) for hosts where multi-out is painful?
- Update cadence for the model itself. The model is the product. If we improve the model, does that ship as a plugin update, a separate model-pack download, or a paid v2?
- Pricing test. $129 versus $149 versus $99. The subscription option might cannibalize one-time at $9/mo. Worth A/B testing in the trial-end flow.
- Watermark beep volume and frequency. Loud enough that the trial converts, quiet enough that the trial actually demonstrates the product. Current proposal of every 30 seconds may be too aggressive for evaluating a long mix.
- AAX requires Avid certification. Timeline and cost of that process is still being scoped and may push v1 ship by four to eight weeks.
- GPU inference. Some producers have capable GPUs sitting idle. Do we ship a Metal and DirectML backend in v1, or stay CPU-only and add GPU in v1.x once we know the demand and the support cost?
- Minimum macOS and Windows versions. macOS 12 covers most working producers but excludes a long tail. Windows 10 is similar. Where do we draw the line and what is the support story for users below the line?
- Beta testing channel. Public beta in the producer community gets us bug reports fast but also gets us leaks and reviews of unfinished work. Closed beta with 50 named producers may be the safer path.
- Localization. v1 is English only. Spanish, Japanese, German, and Portuguese-BR are real producer markets. When do we invest, and is plugin GUI localization worth the QA overhead versus just localizing the website and docs?
- Educator licensing. Berklee, ICON Collective, and similar schools will ask for site licenses. Do we ship a separate education SKU or handle it as a sales conversation in v1?
- Marketing pre-launch. Plugin Boutique placement, Loopcloud bundle, KVR coverage, Sweetwater. Which channels do we commit to and which do we ignore for v1?
