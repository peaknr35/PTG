---
type: foundation (the rules every feature and output must trace to)
project: PTG (Practice-to-Game Lab)
status: draft v0.1.1
updated: 2026-11-11
changes: v0.1.1 (2026-11-11) marks three checks (P7, P9, P10) as "not built yet" because the files they name don't exist. No principle changed.
evidence: 02_research/library.md (shelf IDs below point there)
rule: if a feature or output can't name the principle it rests on, the problem is the foundation, not the feature
---

# PTG Foundation

**Why this file exists:** adding features is easy. Knowing *why* a feature exists, and whether its output meets the standard, is the hard part. Every feature, prompt, rule and mentor output in PTG must point back to a principle here. If it can't, stop and fix this file first.

---

## 1. The model everything rests on

```
KNOW  — understanding what a good play is        (film study builds this)
SEE   — spotting the cue in real time            (freeze-frame, player's-eye view)
DO    — executing under pressure                 (practice that carries game information)
GAME  — tests SEE + DO together                  (the only real proof)

PTG's job: move a player from KNOW to SEE to DO, and check it in GAME.
```

The founder's lived problem: KNOW grew from film study, SEE and DO didn't, so games never changed.

---

## 2. Evidence strength scale

| Grade | Means | Allowed wording |
|---|---|---|
| **A** | Meta-analysis or randomized trials, with transfer tested | "Research supports..." |
| **B** | Small or few studies, youth only, or one sport | "Early research suggests..." |
| **C** | Coaching practice, theory, or our own hypothesis | "Coach's call..." / "Worth testing..." |
| **P** | Proven for *this* player by their own delayed retest | "This has held for you..." |

Grades come from the library's shelves. Nothing in PTG is graded A yet for basketball specifically.

---

## 3. The principles

Each one has: **the rule → why (evidence + grade) → what it forbids → how we check it.**

### P1. Every claim wears a label
- **Rule:** every statement is OBSERVED, INFERRED, HYPOTHESIS or UNKNOWN, plus an evidence grade.
- **Why:** sports-AI research calls for grounding claims in evidence (Survey of Large Models in Sports, READ). Fluent text isn't evidence.
- **Forbids:** unlabeled advice; "you missed because..."
- **Check:** the claim audit scans every output for missing labels.

### P2. Practice must carry game information
- **Rule:** a drill is judged by whether it includes the information the game will demand (a cue, a decision, time pressure), **relative to the drill's stated purpose.**
- **Why:** representative design; VR/AR only transfers when interactions, viewpoint and scenarios match (Sports Medicine 2023, shelf 1, B). Gravino 2026, youth attention (shelf 1, B).
- **Forbids:** judging a comfortable-mechanics drill as "bad" for lacking a defender; a single "representativeness %" as the goal.
- **Check:** every drill record has a `purpose` field; the audit compares against the purpose.

### P3. Pause before the decision
- **Rule:** film lessons freeze **before** the outcome, and the player commits to an answer before the reveal.
- **Why:** temporal occlusion training meta-analysis: large effect, transfer to both video and field tests (Müller 2024, shelf 1b, **A−**, sport-general). This is the best-supported principle PTG has.
- **Forbids:** showing the play and then explaining it (passive film).
- **Check:** every film lesson has a pause point and a logged player answer.

### P4. Test in a context the player hasn't trained
- **Rule:** learning is only claimed if it shows up on **held-out** clips and, later, in games.
- **Why:** plain video decision training transferred narrowly (PLOS, shelf 1, B). Retention and transfer are different tests (Human Kinetics, shelf 3).
- **Forbids:** "you've learned this read" based on the clip you trained on.
- **Check:** each lesson has ≥2 held-out clips and a game assignment.

### P5. Train from the player's seat (open question)
- **Rule:** when possible, show the play the way the player saw it (cropped, lower, limited view), not just the broadcast seat.
- **Why:** broadcast-video training showed no change while 360° VR improved decisions (Kittel 2020, B). But a 2024 softball study found **no difference** (B). Mixed.
- **Status:** HYPOTHESIS. PTG tests it (same lesson, broadcast vs. cropped view) before treating it as a rule.
- **Forbids:** claiming player's-eye view "works" until our own comparison says so.

### P6. Visualize yourself, not the pro
- **Rule:** imagery and modeling use **the player's own footage** (and, later, edited "better self" clips) together with observation + imagery; the pro is the *source of the read*, not the body to copy.
- **Why:** feedforward self-modeling + observation/imagery beat skilled-model video for children's free throws (Isfahan 2025/26, C+/B−). Imagery + observation beat observation alone (Korea, B−). PETTLEP: imagery must match the real environment and perspective (2022 update, B).
- **Forbids:** "train like Curry" as copying volume or mechanics; imagery scripts set in the empty gym.
- **Check:** every pro-film lesson includes "the beginner/intermediate version of this read" and an imagery script set in a **game** context.

### P7. Cues lean external, but it's tested, not law
- **Rule:** default to cues about the effect (rim, ball path, floor), and test against internal cues per player.
- **Why:** external focus protected free throws under fatigue (2025 studies, shelf 2, B). **No basketball retention meta-analysis exists** (gap found).
- **Forbids:** a blanket ban on internal cues; "proven" labels without a retest.
- **Check (not built yet, no `cue-library.md` exists):** `cue-library.md` entries carry a grade; the player's results upgrade a cue to P.

### P8. Measure only what the sensor can measure
- **Rule:** a number appears only if PTG has validated how it's measured. Otherwise it's labeled "estimate" or not shown.
- **Why:** phone pose accuracy is joint- and task-specific; it's validated for jumps and running, **not for jump-shot angles** (shelf 2b, B). Errors of several degrees can be larger than the make/miss difference.
- **Forbids:** precise-looking joint angles from one phone; "you forgot this angle" stated as fact.
- **Check:** each measured feature has a validation note (phone vs. slow-mo hand check). Allowed now: makes/misses, catch-to-release by frame count, landing spot vs. a floor mark.

### P9. A change is only real if it's bigger than normal variation
- **Rule:** compare results against the player's own session-to-session noise. "Inconclusive" is a valid answer.
- **Why:** single-athlete change methods use the test's own variability (Turner, N = 1, shelf 3, B). N-of-1 alternating designs (shelf 3).
- **Forbids:** "it worked" from one good session.
- **Check (not built yet, no `noise_band.py` exists):** `noise_band.py` runs before any comparison is reported.

### P10. Lessons need a delayed retest before they're kept
- **Rule:** a lesson enters the player's memory only after it holds on a later retest **without the cue**.
- **Why:** retention is the test of learning (shelf 3). Agent memory can **hurt** as well as help (EvoMemBench 2026, shelf 7, B).
- **Forbids:** saving every insight as a fact.
- **Check (not built yet, no `memory/lessons.md` exists):** `memory/lessons.md` entries link to the retest session.

### P11. Support must fade
- **Rule:** cues, replays and mentor prompts get less frequent as the player improves. The player chooses when to see video feedback.
- **Why:** self-controlled video feedback helped learning the set shot (Aiken 2012, B). Systems trap: shifting the burden.
- **Forbids:** constant replay; a mentor the player can't play without.
- **Check:** each experiment has a fade step; replay is on request.

### P12. Code calculates, the model explains, and nobody invents
- **Rule:** numbers come from scripts over logged data; the LLM only explains structured results it was given.
- **Why:** basketball agents do better composing specialized tools with evidence bindings and stopping conditions, and tool errors carry through (BasketballBench 2026, READ). LLMs do well only on simple structured tasks (shelf 6).
- **Forbids:** the LLM producing counts, angles, percentages or invented "what Curry did."
- **Check:** every number in an output traces to a script output file.

### P13. One priority at a time, held until its gate
- **Rule:** the mentor works on one priority, keeps it until its progression gate passes or fails, and never flips on one session or one game.
- **Why:** delay mismatch (skill changes over weeks, sessions are daily) causes overcorrection; a long list overflows the player (systems: buffers + delays).
- **Forbids:** a list of every visible flaw; changing the plan after one bad game.
- **Check:** each priority has a start date, a gate, and a minimum hold.

### P14. Pros are sources, not answer keys
- **Rule:** a pro's play shows *a* good read; more than one decision can be right; the pro's private routine is never invented.
- **Why:** documented practice ≠ interpretation ≠ personal adaptation. Rights and likeness rules on real players' names and footage.
- **Forbids:** "Curry thought..."; using broadcast footage without rights; speaking *as* a real player.
- **Check:** every pro reference is tagged documented / interpretation / adaptation, with a source.

### P15. Separate "window closed" misses from "had time, still missed" misses, only when the math is robust
- **Rule:** for a contested shot, classify the miss as a **window error** (the defender's arrival came before release, so the read or the decision is the issue) or an **execution error** (there was time and it still missed), but **only if the sign of the timing margin holds across the plausible range** of the estimated inputs (defender speed, boundary). If the sign flips inside that range, report "can't tell."
- **Why:** this separates SEE/decide from DO, which is PTG's core model (§1). The timing math is simple and inspectable (03_design/boards/v2-honest-model.html). But defender speed and the contest boundary are **estimates** from one phone (P8), so a single number would let the guess make the diagnosis.
- **Status:** HYPOTHESIS (C). A diagnostic frame, not a validated classifier.
- **Forbids:** a fixed 3.0 ft boundary; one-number margins from guessed speeds; "root fault isolated."
- **Check:** every margin is shown as a range; the label appears only when the whole range agrees.

---

## 4. The output standard (what every mentor card must contain)

```
PRIORITY:        one thing
BASIS:           what we saw (OBSERVED/INFERRED) + which sessions/clips
WHY IT MATTERS:  the game moment it shows up in
RATIONALE:       principle IDs (e.g. P3, P6) + evidence grade
WHERE TO LOOK:   the information to watch before acting (general support: experts fixate
                 longer on informative areas — Sci Reports 2023, B; which areas = coach-annotated, C)
SHOW:            a paired clip — the player's attempt next to a demonstration of the fix
                 (ExpertAF pattern: words + a shown correction; demo must not imply a measured angle, P8)
WORKOUT:         fits the player's space, time and partner
GAME ASSIGNMENT: what to look for next game + the 10-second tag to log
GATE:            what must be shown before moving on
STATUS:          coach's call → testing → held (P) / dropped
```

A card missing any field doesn't ship. That's what "accurate to the standard" means in PTG.

---

## 5. The feature admission test

Before any feature gets built, answer all five:

1. **Which principle(s) does it serve?** (P-numbers.) None → don't build it.
2. **Which part of KNOW → SEE → DO → GAME does it move?**
3. **What's its sensor, and is it validated?** (P8)
4. **How will we know it's wrong?** (The observation that would make us remove it.)
5. **Does it close a loop or open a new one?** A feature that adds output without feedback gets rejected.

---

## 6. How current features map to the foundation

| Feature | Moves | Principles | Weakest link |
|---|---|---|---|
| Film to Floor: pro mode (pause → choose → reveal → beginner version → rehearse) | KNOW → SEE | P3, P4, P5, P6, P14 | Content rights + coach annotation time |
| Film to Floor: your-film mode (side-by-side makes/misses) | SEE → DO | P8, P9, P1 | Phone measurement validity |
| Mentor prescription card | DO → GAME | P1, P12, P13 + output standard | Who reviews the advice |
| Practice audit (purpose-aware) | DO | P2 | No target mix defined yet |
| Cue experiments (One Cue / N-of-1) | DO | P7, P9, P10, P11 | Noise band needs 2+ weeks of data |
| Race Clock (03_design/boards/v2-honest-model.html) | KNOW | P1, P8 | Inputs mostly estimates |
| Game tags | GAME | P4 | Players forget to log |

---

## 7. How the foundation changes

- New research enters the **library** as a card first (graded, with "what this does NOT justify").
- A principle changes only through a **reviewed revision**: bump the version, date it, and note what changed and why.
- When evidence conflicts (like P5), the principle becomes a HYPOTHESIS and PTG designs a test. It doesn't pick a side.
- The player's own P-grade results can upgrade a principle *for that player*, never for everyone.
- Review every principle at least every 6 weeks (ICM: stale context is an anti-pattern).
