---
type: run guide
status: ready
created: 2026-11-10
goal: fix the event definitions, not prove anything
time: ~1 hr recording + ~1 hr marking (pass 1) + ~45 min (pass 2, next day) + 20 min debrief
---

# Rehearsal 01: Five-Clip Annotation Rehearsal

**What this answers:**
1. Does your phone record a steady frame rate?
2. Do the event definitions hold up on real clips?
3. Can you repeat your own marks a day later?

**What it does NOT answer:** anything about your shot, your decisions or transfer. Five clips is a **measurement rehearsal**, not a study.

## Files in this folder
| File | Use it |
|---|---|
| `predictions.md` | **First.** Before recording |
| `capture-checklist.md` | While setting up and recording |
| `event-definitions-v0.md` | Before and during marking |
| `ledger.csv` | While marking (opens in Google Sheets or Excel) |
| `debrief.md` | After pass 2 |

## The run, step by step

**Day 1**
1. Fill in `predictions.md` (5 minutes, no peeking at anything else).
2. Set up per `capture-checklist.md`. Do the **clock test** first.
3. Record the 5 clips:
   | Clip | Condition | What happens |
   |---|---|---|
   | 01 | solo | Self-pass or toss, catch-and-shoot, no one else |
   | 02 | solo | Same as 01 |
   | 03 | simulated | Same, but a partner calls "go" or a phone beeps as the cue |
   | 04 | live | A partner passes and closes out from a marked spot |
   | 05 | live | Same as 04 |
   No partner? Do 01–03 now and 04–05 later. The ledger still works.
4. Copy the **original files** to the computer (AirDrop/USB, choosing "original" or "most compatible off"). **Don't** send them through messaging apps, which re-compress.
5. Get the timestamps for each clip (see "Timestamps" below).
6. **Pass 1:** mark every clip in Kinovea per `event-definitions-v0.md` and fill the P1 columns.

**Day 2 (at least 24 hours later)**
7. **Pass 2:** mark again in a **different order** (for example 04, 01, 05, 03, 02), **with the P1 columns hidden.** Fill the P2 columns.
8. Compute the margins and differences (formulas are at the bottom of `ledger.csv` notes).
9. Fill in `debrief.md`. Then revise the definitions → save as `event-definitions-v1.md` and freeze it.

## Timestamps (variable frame rate check)
Install FFmpeg (ffmpeg.org; it includes `ffprobe`). For each clip, run in a terminal:

```bash
ffprobe -v error -select_streams v:0 -show_entries stream=r_frame_rate,avg_frame_rate,time_base,nb_frames -of default=noprint_wrappers=1 clip01.mov
```

```bash
ffprobe -v error -select_streams v:0 -show_entries frame=best_effort_timestamp_time -of csv=p=0 clip01.mov > clip01_pts.csv
```

- The second file has **one line per frame**: line 1 = the first frame's time in seconds.
- Open it in Sheets and add a column for the difference between each line and the one before (the frame interval). If those intervals are all about the same → **CFR**. If they vary or jump → **VFR**.
- **Check once:** is Kinovea's first frame "0" or "1"? Step to the very first frame in Kinovea and note the number it shows. That tells you how Kinovea frame N maps to line N or N+1 in the timestamp file. Write it in the ledger notes.
- If FFmpeg feels like too much right now, skip it and write `PTS: not checked` in the ledger. You'll still learn about the definitions, but the margins become **"assumes CFR."**

## Rules
- Mark only what you can see. When unsure, write **NO** (not observable). Never guess.
- Don't change a definition mid-pass. Write the problem in the notes and fix it in v1.
- You know whether your own shots went in. For pass 2, try to stop playback at release so you don't re-watch the result, which helps a little.
