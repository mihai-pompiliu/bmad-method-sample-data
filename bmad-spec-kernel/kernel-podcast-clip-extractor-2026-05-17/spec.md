# Podcast Clip Extractor Kernel

## Problem

An indie podcaster finishes recording and editing a 60 to 120 minute episode, then needs to manually scrub the full audio to find shareable moments for social promotion. This eats one to three hours per episode and is the most common reason promotional clips never ship.

## Capabilities

- Accept a single finished episode audio file (mp3 or wav) as input and report when ingestion is complete.
- Produce a ranked list of 3 to 5 candidate clips, each between 30 and 90 seconds, with start timestamp, end timestamp, and a one-sentence reason the moment was selected.
- Output an accompanying transcript excerpt for each candidate clip.
- Allow the user to reject a candidate and request a replacement from the next-best ranked moment.
- Export the accepted clips as individual audio files named by episode and timestamp.

## Constraints

- Must process a 90 minute episode end to end in under 4 minutes on a 2023 MacBook Air (M2, 16GB RAM).
- Must run fully offline after first-time model download.
- Candidate clips must begin and end on sentence boundaries, not mid-word.
- Total disk footprint of installed tool plus models must stay under 5 GB.

## Non-goals

- No auto-posting or scheduling to any social platform.
- No video generation, waveform visuals, or captioned video clips.
- No real-time or live-episode processing.
- No multi-episode batch mode in this version.
- No transcript editing UI.

## Success signal

The podcaster ships promotional clips for at least 4 of every 5 episodes within 24 hours of finishing the edit, measured over 8 consecutive episodes.
