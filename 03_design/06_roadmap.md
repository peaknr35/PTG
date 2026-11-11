---
type: plan + roadmap + to-do (the ONE home for these; other docs link here)
project: PTG (Practice-to-Game Lab)
version: v1.0
updated: 2026-11-10
replaces: 01_blueprint.md §9–10 (roadmap, to-do) and 04_cv-blueprint.md §10 (sensor phases are now inside Phase 1 and 4 below)
---

# PTG Roadmap

## 1. The plan in five lines
1. **PTG is a learning lab and a portfolio**, not the income source (`01_foundation/goal-system-map.md`).
2. **It owns one problem nobody else closes:** practice that never gets checked against games.
3. **It moves by evidence, not by calendar:** each phase has a gate, and you move on only when the gate passes.
4. **Build by hand first, then automate only what repeats** (`03_skills-architecture.md` §6b).
5. **Every phase leaves two things behind:** something learned (judgment log) and something reusable (a template for client work).

## 2. What makes PTG stand out (the anti-copycat checklist)
Before adding any feature, it must strengthen at least one of these. If it only copies what competitors do, cut it.

| PTG's edge | Competitors stop at |
|---|---|
| **The whole chain**: pro read → beginner version → your own film → floor rep → game assignment → game tag → retest | One link each (tracking, plans, quizzes, VR) |
| **Game check built in**: no lesson counts until it shows up in a game tag | Practice results only |
| **Evidence grades on every card**: coach's call / early research / proven for you | Confident advice with no label |
| **Lessons promoted only after a delayed retest** | Every insight saved as fact |
| **Your own film is the textbook** (legal, personal) | Pro clips or none |
| **Honest measurement**: ranges, "can't tell," validated sensors only | Precise-looking numbers |

## 3. Roadmap (gated phases)

| Phase | What happens | Code? | Gate to move on | Leaves behind (reusable) |
|---|---|---|---|---|
| **0. Think + measure by hand** ← *now* | Tutor sessions 1–7 (one question at a time) · rehearsal-01 (5 clips, 2 passes) · read the 3 key papers | No | You can explain the problem without AI · event definitions v1 frozen · CFR/VFR known for your phone | Rehearsal template · glossary template |
| **1. Ground truth** | 20-rep session with definitions v1 · count closeouts in 3 pickup runs | No | ≥90% of events and margins agree within 2 frames (G1) | Labeling QC protocol, filled once |
| **2. Feature 01 by hand** (practice vs. game) | Your practice clips vs. your game tags · one hypothesis · one experiment · delayed retest | No | One full loop completed and logged; one lesson held or dropped | The case study's first data |
| **3. Feature 05 by hand** (One Cue) | External vs. internal cue, ABBA blocks, retest without the cue | No | Noise band known (2+ weeks); result honest, even "inconclusive" | N-of-1 experiment template |
| **4. Spec for an agent** | Numbered BR → FR → tests · stage contracts with DERIVE/INFER labels · claim-audit rules · case matrix | No | Every feature passes the admission test (`foundation.md` §5) | Spec + contract templates |
| **5. Agent builds v1** | clip-intake · stats-and-noise · claim-audit first, then the Race Clock on your real data | **Yes** | Your acceptance tests pass; you can trace one card end to end (PRIMM) | 3 reusable skills for client work |
| **6. Feature 02 + pilot** | Read Before React on your own film · 5 adult players for 4–6 weeks | Yes | 4 of 5 can explain a card's grade; game tags actually get logged | Pilot report |
| **7. Decide** | Continue, simplify or change direction · IP check with an attorney | — | A written decision with evidence | Portfolio piece |

**Redirect rules (decide now, not later):**
- Rehearsal marks don't repeat within 2 frames → fix camera or definitions before Phase 1.
- Fewer than ~3 closeouts per pickup game → pick a more frequent situation.
- A lesson promotes too fast → tighten the retest rule.
- Two weeks with no real clip, tag or conversation → stop designing and go do the next small loop.

## 4. Parallel track: the business (your actual income)
| Step | Done when |
|---|---|
| Write the "How I audit AI-built systems" case study (source: `_archive/chat-transcripts/claude-session-01_full-transcript.zip`) | One page with 5 real catches |
| Turn the method kit into blank templates (foundation, glossary, contract, ledger) | 4 templates without basketball words |
| Pick one buyer group you can reach | Named in writing |
| 5 problem conversations (no pitching) | 5 notes on "the last time this happened" |
| Set an hour cap and protect family time | On the calendar |

## 5. To-do: the next 14 days
**This week**
- [ ] Answer tutor question 1 (a real game that went wrong, and what you practiced after)
- [ ] Fill `04_experiments/rehearsal-01/predictions.md`
- [ ] Record clips 01–03 (solo, solo, simulated); 04–05 when a partner is free
- [ ] Run ffprobe on one clip: CFR or VFR?
- [ ] Pass 1 marking in Kinovea
- [ ] Re-download `s40279-023-01884-3.pdf` into `02_research/papers/01_skill-science/`

**Next week**
- [ ] Pass 2 marking (different order, P1 hidden)
- [ ] Fill `debrief.md`; write `event-definitions-v1.md`; update `_templates/rehearsal/`
- [ ] Read: temporal occlusion meta-analysis (Müller 2024); write its evidence card
- [ ] Draft the AI-audit case study outline
- [ ] Name one buyer group; book the first conversation

## 5b. Learning reps (PRIMM: predict, run, investigate, modify, make)
These keep you in control of AI-written code. Do one per phase. **The gate is "can I predict its behavior and explain a failure?", not "can I recognize the code?"**

**Rep 1: shot-record calculator** (Phase 0, no code yet)
| Shot ID | Outcome |
|---|---|
| A01 | Make |
| A02 | Miss |
| A03 | Make |
| A04 | Unknown |
| A02 | Miss |

1. **Write your own policy first** (it's your product decision, not the AI's): Do unknowns count? Is a repeated ID a new attempt or a duplicate? What if the same ID is both make and miss? What shows when there are no known outcomes?
2. **Predict:** unique attempts · known outcomes · makes · percentage · warnings · and what % unknown would make you distrust the session.
3. Systems checks to add to your policy: unknowns may be **mostly misses** (ball out of frame after a bad miss), which would inflate the %; report **% unknown** next to every %.
4. Only then ask an agent to implement **only** this calculator, with validation separate from the math, and no LLM, camera or interface. Find the lines that dedupe, exclude and calculate; change one input and predict again.

**Rep 2:** `03_design/boards/v2-honest-model.html`: predict what changes when the contest boundary goes from 3 ft to 5 ft (arrival, margin, separation at release, the release dot), then check.

## 6. How we'll know it's working
| Signal | Healthy (prediction) |
|---|---|
| Prediction accuracy in your judgment log | Rises over runs |
| Mark agreement (pass 1 vs. 2) | Reaches the G1 target by Phase 1 |
| Docs written vs. clips recorded | Clips catch up; docs stay flat |
| Reusable templates created | One per phase |
| Business conversations held | 5 in the first month |
